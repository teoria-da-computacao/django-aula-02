# pw-aula-django-02

# Web app Tarefas 
(app construída na aula de 17.5)

1. Aplicação para gestão de tarefas, permitindo, criar, alterar e apagar tarefas

### Base de dados
1. A aplicação tem como base o ficheiro [models.py](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/models.py) onde se define a classe Tarefa, que se materializará numa tabela na base de dados, para armazenar tarefas.
1. Na aplicação admin (http://127.0.0.1:80007admin/) podemos editar tarefas (para aceder a admin deve criar um superuser `python manage.py createsuperuser`)
1. Na consola Python pode também manipular os dados da tabela com queries. Veja [exemplo](#queries) em baixo, e consulte [djangoproject](https://docs.djangoproject.com/en/3.2/topics/db/queries/) para mais detalhes.

### Aplicação DJango
1. Em [`views.py`](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/views.py) existem 4 views que permitem listar, criar, editar e apagar tarefas.
1. Em particular, as funções view para editar e apagar recebem como argumento, além do request, a primary-key da respetiva tarefa.  


### Classe TarefaForm
1. Em [`forms.py`](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/21a2f865f02eeb36007ac3e4916cc0dc69835c6b/tarefas/forms.py) é definida a classe TarefaForm, classe de formulário criado com base na classe Tarefa. Uma forma muito eficiente e simples de criar um formulário. É possível customizar vários campos:
    * [`labels`](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/21a2f865f02eeb36007ac3e4916cc0dc69835c6b/tarefas/forms.py#L18) a ser apresentadas em substituição do nome do atributo da classe 
    * [`widgets`](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/21a2f865f02eeb36007ac3e4916cc0dc69835c6b/tarefas/forms.py#L11) permitem especificar pares propriedade=valor do elemento HTML `<input>` de um determinado campo do formulário; valores para propriedades tais como `class`, `placeholder`, valores `max` e `min`. 
    * [`help_texts`](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/21a2f865f02eeb36007ac3e4916cc0dc69835c6b/tarefas/forms.py#L25) especificam texto auxiliar dum determinado campo do formulário.
1. Esta classe é usada para criar um objeto formulário vazio, ou criar um objeto formulário preenchido com dados enviados pelo utilizador através de um template. 

### Formulário
3. A classe TarefaForm serve para criar um [formulário vazio](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/views.py#L17) a inserir no template [`nova.html`](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/templates/tarefas/nova.html#L8).


### Templates
1. Os templates usam como layout base [`base.html`](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/templates/tarefas/base.html), que é estendido por todos os templates da aplicação
1. O template base está estilizado por um CSS criado, [`base.css`](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/static/tarefas/base.css). Também são usadas algumas classes Bootstrap (importadas através de um [link](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/templates/tarefas/base.html#L7)). Usam-se as classes jumbotron (para header) e btn para formatar botões.


### Passos para lançar e editar a aplicação
1. Abra a linha de comandos (PowerShell ou cmd)
1. Descarregue uma cópia (clone) do repositório com o comando `git clone https://github.com/ULHT-PW-2020-21/pw-django-01` 
1. Entre na pasta  `cd pw-django-02`
1. Garanta que tem o pipenv instalado, correndo o comando `python3 -m pip install pipenv`
1. Crie um ambiente virtual `pipenv install django` 
1. Active o ambiente virtual `pipenv shell`
1. Lance a aplicação no browser com o comando `python manage.py runserver`. 
1. Tem disponíveis as aplicações hello no link `http://127.0.0.1:8000`
1. abra a pasta com o Pycharm, para a explorar.
1. devera criar um superuser `python manage.py createsuperuser` para pode aceder ao modo admin e ditar diretamente a base de dados

### Queries

```Python

from tarefas.models import Tarefa

t1 = Tarefa(titulo='Ir correr', prioridade=2) # cria nova tarefa
t1.save()
t2 = Tarefa(titulo='Ir ao cinema', prioridade=2) # cria nova tarefa
t2.save()
t3 = Tarefa(titulo='Fazer laboratório de PW', prioridade=2) # cria nova tarefa
t3.save()
t4 = Tarefa(titulo='Ir passear', prioridade=1); t4.save()  # cria nova tarefa

t3.prioridade = 3; t3.save() # alterar valor de atributo

tarefas = Tarefa.objects.all()   # obtém QuerySet de todos os objetos da tabela
t1.delete() # apaga t1 objeto da tabela
Tarefa.objects.all().delete() # apaga todos os elementos da tabela
```
