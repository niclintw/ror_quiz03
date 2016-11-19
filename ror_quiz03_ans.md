1. 請解釋 database.yml, routes.rb, 和 Gemifle 分別是什麼？ 他們分別在一個 Rails 專案裡的什麼位置？ 他們為什麼對一個 Rails 專案如此重要？

Answer：
```
  (1).database.yml：
    位置： (Rails專案/config/database.yml)

  (2).routes.rb：設定你所想要顯示URL內容且對應檔案的地方
    ex:
       get 'users/hallo' => 'users#hallo'
       http://localhost:3000/users/hallo 顯示的內容是對應到users下的hallo
    位置：(Rails專案/config/routes.rb)
 
  (3).Gemfile：放置會使用到的軟體以及版本, 若有新增軟體要bundle install.
    位置：(Rails專案/Gemfile)
```

***

2. MVC 架構裡的 M, V, 和 C 分別代表什麼？ 

Answer：
```
   (1). M 代表 model
   (2). V 代表 view
   (3). C 代表 controller
```
***
   
3. 請解釋 CRUD 是哪四個字的縮寫？

Answer：
 ```
   (1). C --> create
   (2). R --> read
   (3). U --> update
   (4). D --> delete
 ```
 
***
   
4. 請問在 routes.rb 裡面加入以下程式碼會產生出哪一些 url？ (提示：在 browser 輸入```http://localhost:3000/rails/info/routes```)
	```ruby
	resources :users
	```

Answer：

    http://localhost:3000/users 

| HTTP Verb     | PATH (url)      | Controller#Action | 目的                 |
| :------------ |:---------------:|:-----------------:| --------------------:|
| GET           | /users          | users#index       | 顯示所有users        |
| GET           | /users/new      | users#new         | 回傳建立新user的表單 |
| POST          | /users          | users#create      | 建立新user           |
| GET           | /users/:id      | users#show        | 顯示特定user         |
| GET           | /users/:id/edit | users#edit        | 回傳編輯user的表單   |
| PATCH/PUT     | /users/:id      | users#update      | 更新特定user         |
| DELETE        | /users/:id      | users#destroy     | 刪除特定user         |

***

5. 請解釋 model 檔案和 migration 檔案的差別

Answer：
	```
    (1). model可以獲取資料庫裡的資料, 把原不相關的資料庫做連結
    (2). migration可以使用ruby的語法去建立資料庫裡所需要的資料表
	```
	
***

6. 若今天發現一個 migration 檔寫錯，請問我應該用什麼指令回復到上一個版本的 migration? 

Answer：
```ruby
    rake db:rollback
```

***

7. 假設今天
	* 我要在資料庫裡產出一個叫 group 的資料表
	* 裡面包括的欄位名稱和相對應的資料型別是： 
		**name (string)**,
		**description (text)**,
		**members (integer)**
    * 請寫出一個能產生出以上需求的 migration 檔

Answer：
  ```ruby
  class CreateGroups < ActiveRecord::Migration
    def change
      create_table :groups do |t|
        t.string :name
        t.text :description
        t.members :integer

        t.timestamps null: false
      end
    end
  end
  ```

***

8. 請解釋什麼是 ActiveRecord? 

Answer：
```
Active Record 是 MVC 的 M（Model），表現商業邏輯與資料的層級。
Active Record 負責新增與操作需要持久存在資料庫裡的資料。
Active Record 本身是物件關聯映射（Object Relational Mapping）系統的描述，以 Active Record 模式實作。

在 Active Record 模式裡，物件擁有持久化的資料與行為，Active Record 確保存取資料的邏輯是物件的一部分，
進而教導使用者如何將物件寫入於讀出資料庫。

Active Record 賦予我們許多功能，最重要幾個是：
* 表示 Model 與資料。
* 表示 Model 之間的關係。
* 表示相關 Model 之間的繼承關係。
* 持久化資料存入資料庫的驗證。
* 以物件導向的風格操作資料庫。

Active Record 預設使用某種命名慣例來找出 Model 與資料表的對應關係。
Rails 會將類別名稱轉成複數來找到對應的資料表。
所以 Book 類對應的資料表便叫做 books。
Rails 單複數轉換機制非常強大，能從單數轉複數、複數轉單數，單字的單複數形的不規則轉換，都能正確處理。
類別名稱由兩個以上的單字組成時，Model 名稱應要遵循 Ruby 的命名慣例，採用駝峰式命名，
而資料表名稱必須採用底線分隔。
```

***

9. 若今天需要為 ```Project``` 和 ```Issue``` 這兩個 Model 建立一對多的關係，請寫出實作上所需要的 migratiion 和 model 檔案 

Answer：
  ```ruby
  class AddProjectAndIssueTables < ActiveRecord::Migration
  	def change
	  	create_table :projects do |t|
	      t.string :name
	    end

	  	create_table :issues do |t|
	      t.string :name
	      t.integer :project_id
	    end
  	end
  end

  class Project < ActiveRecord::Base
  	has_many :issues
  end

  class Issue < ActiveRecord::Base
  	belongs_to :project
  end
  ```

***

10. 若今天我有以下 model 檔：

  ```ruby
  class User < ActiveRecord::Base
    has_many :groups_users
    has_many :groups, through: :groups_users 
  end
  ```

  請寫出和這個 model 檔相關連的 model 檔，以及這些 model 檔所需要的資料庫欄位

Answer：
  ```ruby
  class Group < ActiveRecord::Base
	has_many :groups_users
	has_many :users, through: :groups_users
  end

  class GroupsUser < ActiveRecord::Base
	belongs_to :group
	belongs_to :user
  end
  ```

***

11. 延續第10題，如果需要讓一個叫 "Bob" 的使用者產生一個名字叫做 "Rails is Fun" 的社團，應該如何在 rails console 裡實作出來？

Answer：
  ```ruby
  bob = User.create(name: "Bob")
  group = Group.create(title: "Rails is Fun")
  bob.groups << group 
  ```
  或是
  ```ruby
  bob = User.create(name: "Bob")
  group = Group.create(title: "Rails is Fun")
  group.users << bob
  ```

***

12. 延續第11題，請寫一段程式碼確保使用者在建立新社團時社團名不可以是空白，而且不能超過50個字

Answer：
  ```ruby
  class Group < ActiveRecord::Base
	has_many :groups_users
	has_many :users, through: :groups_users

	validates :title, presence: true, length: {maximum: 50}
  end
  ```


