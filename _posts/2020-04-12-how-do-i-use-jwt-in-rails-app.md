---
layout: post
title: Tôi đã dùng Json Web Token trong Rails như thế nào?
image: /img/jwt/jwt.png
---

Hiện nay xu hướng phát triển app trên điện thoại và có trang quản lý bằng web là một xu hướng rất phổ biến, được rất nhiều nhà phát hành lựa chọn để xây dựng nên một phần mềm mang tới cho người dùng. Ruby on Rails cũng thường được lựa chọn để làm nền tảng back-end cho các phần mềm này, và phương thức xác thực phổ biến là dựa vào token-based.
Trong bài viết này, tôi sẽ cung cấp tổng quan cách để sử dụng Json Web Token trong Rails api.

**Chuẩn bị:**
- Ruby 2.6.5
- Rails 6.0
- MySQL
- Postman

**REST API table**

| URL / ENDPOINT | VERB | DESCRIPTION |
| :------ |:--- | :--- |
| /auth/login | POST | Generate token |
|/users | POST | Create user |     
|/users | GET | Return all users |
|/users/{username} | GET | Return user |      
|/users/{username} | PUT | Update user |      
|/users/{username} | DELETE | Destroy user | 

Bây giờ, ta bắt đầu tạo rails api project
`rails new rails-jwt --api`

Thêm gem Json Web Token(JWT) và bcrypt

- JWT: dùng để encode/decode token vói thời gian hết hạn cho trước
- bcrypt : mã hóa password

```
gem 'jwt'
gem 'bcrypt'
```

sau đó dùng `bundle install` để  cài đặt gem

Thêm vào routes

```
# config/routes.rb
Rails.application.routes.draw do
  resources :users, param: :_username
  post '/auth/login', to: 'authentication#login'
  get '/*a', to: 'application#not_found'
end
```
Ta dùng resources :users để tạo ra các route cho users theo chuẩn REST API như bảng ở trên.

**Tạo class JsonWebToken**

```
class JsonWebToken
  SECRET_KEY = Rails.application.secrets.secret_key_base. to_s

  def self.encode(payload, exp = 24.hours.from_now)
    payload[:exp] = exp.to_i
    JWT.encode(payload, SECRET_KEY)
  end

  def self.decode(token)
    decoded = JWT.decode(token, SECRET_KEY)[0]
    HashWithIndifferentAccess.new decoded
  end
end
```

SECRET_KEY là chìa khóa để mã hóa và giải mã token, ta gán secret key được tạo theo mặc định bởi Rails vào biến SECRET_KEY. SECRET_KEY phải bí mật và không được chia sẻ. Mỗi khi thực hiện việc encode/decode bằng JWT, ta đều cần chỉ định SECRET_KEY. Hàm encode và decode ở trên được định nghĩa là hàm tĩnh bởi vì nó sẽ cho phép linh hoạt thực hiện công việc mã hóa và giải mã mà không cần khởi tạo đối tượng JsonWebToken.

Hàm self.encode có 2 tham số, payload và exp. payload có dạng key-value object để lưu dữ liệu mà ta muốn mã hóa. exp là thời gian hết hạn của token, mặc định token sẽ có hiệu lực trong 24 giờ.
Trong hàm self.decode, ta sẽ giải mã token do người dùng cung cấp và nhận giá trị đầu tiên sau đó gán cho biến được giải mã, giá trị đầu tiên chứa payload đã mã hóa trước đó và giá trị thứ hai chứa thông tin về  SECRET_KEY.

**Tạo function authorize_request**

```
class ApplicationController < ActionController::API

  def not_found
    render json: { error: 'not_found' }
  end

  def authorize_request
    header = request.headers['Authorization']
    header = header.split(' ').last if header
    begin
      @decoded = JsonWebToken.decode(header)
      @current_user = User.find(@decoded[:user_id])
    rescue ActiveRecord::RecordNotFound => e
      render json: { errors: e.message }, status: :unauthorized
    rescue JWT::DecodeError => e
      render json: { errors: e.message }, status: :unauthorized
    end
  end
end
```

Hàm authorize_request dùng để xác thực request của người dùng, ta lấy token từ header với key là 'Authorization', từ đó ta có thể giải mã token để lấy ra giá trị payload, ở đây ta lấy ra user_id của người dùng. Không nên đưa dữ liệu thông tin đăng nhập của người dùng vào payload vì nó sẽ gây ra sự cố bảo mật.

Khi decode token nó sẽ bắn ra lỗi JWT::DecodeError nếu như token đã hết hạn, không tìm thấy user, token không hợp lệ. Sau đó ta lưu user_id nhận được từ payload vào biến current_user, nếu người dùng không tồn tại, nó sẽ trả về ActiveRecord::RecordNotFound và nó sẽ hiển thị thông báo lỗi với http status là unauthorized.

**Tạo model user:**
`rails g model user name:string username:string email:string password_digest:string`
và thêm validation

```
class User < ApplicationRecord
  has_secure_password
  mount_uploader :avatar, AvatarUploader
  validates :email, presence: true, uniqueness: true
  validates :email, format: { with: URI::MailTo::EMAIL_REGEXP }
  validates :username, presence: true, uniqueness: true
  validates :password,
            length: { minimum: 6 },
            if: -> { new_record? || !password.nil? }
end
```
**Tạo controller user:**
`rails g controller users`

thêm một số hàm CRUD:

```
class UsersController < ApplicationController
  before_action :authorize_request, except: :create
  before_action :find_user, except: %i[create index]

  # GET /users
  def index
    @users = User.all
    render json: @users, status: :ok
  end

  # GET /users/{username}
  def show
    render json: @user, status: :ok
  end

  # POST /users
  def create
    @user = User.new(user_params)
    if @user.save
      render json: @user, status: :created
    else
      render json: { errors: @user.errors.full_messages },
             status: :unprocessable_entity
    end
  end

  # PUT /users/{username}
  def update
    unless @user.update(user_params)
      render json: { errors: @user.errors.full_messages },
             status: :unprocessable_entity
    end
  end
  ...
end
```

**Tạo controller authentication**
`rails g controller authentication`

áp dụng cho tính năng login

```
class AuthenticationController < ApplicationController
  before_action :authorize_request, except: :login

  # POST /auth/login
  def login
    @user = User.find_by_email(params[:email])
    if @user&.authenticate(params[:password])
      token = JsonWebToken.encode(user_id: @user.id)
      time = Time.now + 24.hours.to_i
      render json: { token: token, exp: time.strftime("%m-%d-%Y %H:%M"),
                     username: @user.username }, status: :ok
    else
      render json: { error: 'unauthorized' }, status: :unauthorized
    end
  end

  private

  def login_params
    params.permit(:email, :password)
  end
end
```

Trong JWT không có cách nào để vô hiệu hóa token, ta có thể  dùng một trong 2 phương pháp sau để triển khai tính năng logout:
1. Xóa token đã được lưu ở client, nhưng token này vẫn hợp lệ, tốt hơn ta nên thiết lập token chỉ có hiệu lực trong thời gian ngắn.
2. Thêm token vào blacklist, khi đó token vẫn hợp lệ cho đến khi hết hạn nhưng ta có thể dựa vào blacklist đó từ chối request đó.

Sau đây là các hình ảnh test trên postman:

Create user
![Create user](/img/jwt/create_user.png)
Login
![Login](/img/jwt/login.png)
Get all users
![Get all users](/img/jwt/get_all_users.png)
Get user
![Get user](/img/jwt/get_user.png)

Happy coding !!!
