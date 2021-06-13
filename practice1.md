## リクエストからレスポンスまでの流れについて(GET 編)

##### ① クライアントからのリクエストが URL（/users)としてアプリケーションのルーティングに届く

<br>
##### ②ルーティングがファイル（routes.rb)に設定されている通りに、URLとHTTPメソッド（/usersだとGET)の組み合わせからコントローラーとアクションを特定する  
resourcesメソッドによりアクションに関するルーティングを一括で設定する
```
(routes.rb)

Rails.application.routes.draw do
root to: 'tasks#index'
resources :tasks
end

```
Request URLとRequest Methodからルーティングがコントローラーとアクションを特定する
[![Image from Gyazo](https://i.gyazo.com/847d96809f536ad7fde325629d65a994.png)](https://gyazo.com/847d96809f536ad7fde325629d65a994)
今回の場合は、ユーザーの一覧表示がしたいので、tasks_controllerのindexアクションを使う
```

(tasks_controller.rb)

class TasksController < ApplicationController
def index
@tasks = Task.all
end
end

```
<br>
##### ③コントローラー内のアクションでモデルとのやりとりを行なってデータベースから必要なデータを取得する
全ユーザーのデータを表示したいので、インスタンス変数の@tasksにallメソッドを使ってデータベースの情報を格納する
```

(tasks_controller.rb)

def index
@tasks = Task.all
end

```
<br>
##### ④ビューの作成
アクションで定義したインスタンス変数の@tasksを使って、ユーザーの名前と作成日時を表示する
```

(index.html.slim)

@tasks.each do |task|
tr
td = link_to task.name, task
td = task.created_at

```
レスポンスとして表示されるHTMLファイル
[![Image from Gyazo](https://i.gyazo.com/f5bf4c72760fb19cd1ae3377e5532966.png)](https://gyazo.com/f5bf4c72760fb19cd1ae3377e5532966)


## リクエストからレスポンスまでの流れについて(POST編)

##### ①クライアントからのリクエストがURL(tasks/new)としてルーティングに届く
<br>
##### ②ルーティングがファイル（routes.rb)に設定されている通りに、URLとHTTPメソッド（/tasks/newだとGET)の組み合わせからコントローラーとアクションを特定する
resourcesメソッドによりアクションに関するルーティングを一括設定
```

(routes.rb)

Rails.application.routes.draw do
root to: 'tasks#index'
resources :tasks
end

```
RequestURLがtasks/new、RequestMethodがGET
[![Image from Gyazo](https://i.gyazo.com/a980f5b4b1483b56677ea61dbcfdc4e1.png)](https://gyazo.com/a980f5b4b1483b56677ea61dbcfdc4e1)
今回は新規登録を行いたいので、tasks_controllerのnewアクションを使う
```

(tasks_controller.rb)

class TasksController < ApplicationController
def new
@task = Task.new
end
end

```
##### ③オブジェクトの作成
newメソッドを使って空のインスタンス変数の@taskを作る
```

(tasks_controller.rb)

def new
@task = Task.new
end

```
##### ④ビューの作成
form_withメソッドを使って、フォームの送信に必要なHTMLを作成する
```

(new.html.slim)

= form_with model: @task, local: true do |f|
.form-group
= f.label :name
= f.text_field :name, class: 'form-control', id: 'task_name'
.form-group
= f.label :description
= f.text_area :description,rows: 5, class: 'form-control', id: 'task_description'
= f.submit nil, class: 'btn btn-primary'

```
##### ⑤新規作成ページでform_withで作られた入力欄にタスク名を打ち込む
##### ⑥作成ボタンを押す
form_withでPOSTメソッドを使うことが設定されている
[![Image from Gyazo](https://i.gyazo.com/739dc277ddcd28a7d294f16bebc08fb9.png)](https://gyazo.com/739dc277ddcd28a7d294f16bebc08fb9)
#### ⑦/tasksに対してPOSTリクエストが送られるとroutes.rbのresources :tasksで対応するtasks_controllerのcreateアクションが実行される
task_paramsで取得したデータをtaskに代入して変数を作成
saveメソッドを使って、データベースにデータを保存
```

def create
task = Task.new(task_params)
task.save!
redirect_to tasks_url, notice: "タスク「#{task.name}」を登録しました"
end

```
##### ⑦ridirect_toメソッドによって、タスクの詳細画面に遷移する
redirect_toメソッドを使って、詳細画面に遷移する
```

redirect_to tasks_url, notice: "タスク「#{task.name}」を登録しました"

```

```
