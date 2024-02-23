#develop/tree 

Тема валидации пользовательских данных всегда является одной из самых сложных и комплексных. Однако, в текущем проекте валидирование информации не будет представлять большой проблемы, т. к. благодаря специфике API-сервера, мы избавлены от необходимости показывать формы, сообщения об ошибках в ней и заниматься прочими вещами, связанными с UI.
По сути, сейчас вам важно разобраться только в трёх основных вещах:

где и как описывать правила валидации
как получать данные от пользователя и применять к ним эти правила
как получать валидированные данные и сообщения об ошибках
Всем этим темам и будет посвящена данная глава.

## Общий подход к валидации в Laravel

Начнём с того, что все данные пользователя изначально сохраняются в объекте запроса. Далее мы вправе создать наследник от класса Illuminate\Foundation\Http\FormRequest. В этом классе мы опишем правила валидации и затем он будет использоваться при обработке всех запросов.
Т.е. принципиальное различие здесь в том, что в Laravel валидируются данные из запроса, а в Yii 2 — сама модель, в которую эти данные уже должны быть загружены.

## Пример класса для валидации запроса
Чтобы разобраться со всем детальнее, давайте рассмотрим пример класса запроса для валидации данных, необходимых для регистрации пользователя:

UserRequest.php
```php
<?php

namespace App\Http\Requests;

use App\Models\User;
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Support\Facades\Auth;
use Illuminate\Validation\Rule;

class UserRequest extends FormRequest
{
    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize()
    {
        return true;
    }

    /**
     * Get the validation rules that apply to the request.
     *
     * @return array
     */
    public function rules()
    {
        return [
            'name' => 'required|string|max:255',
            'email' => [
                'required',
                'string',
                'email',
                'max:255',
                $this->getUniqRule(),
            ],
            'password' => [
                $this->getPasswordRequiredRule(),
                'string',
                'min:8',
                'confirmed',
            ],
            'file' => 'nullable|image|max:10240'
        ];
    }

    private function getUniqRule()
    {
        $rule = Rule::unique(User::class);

        if ($this->isMethod('patch') && Auth::check()) {
            return $rule->ignore(Auth::user());
        }

        return $rule;
    }

    private function getPasswordRequiredRule()
    {
        return $this->isMethod('patch') ? 'sometimes' : 'required';
    }
}
```

Самой главной частью этого кода является массив правил

prev:[[Middlewares]] next:[[RBAC]]
