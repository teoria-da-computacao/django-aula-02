# pw-aula-django-02

# Web app Tarefas (aula de 17.5 de Programação Web)

### Breve explicação
1. Aplicação para gestão de uma lista de tarefas, permitindo, criar, alterar e apagar tarefas
1. A aplicação tem como base o ficheiro [models.py](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/models.py) onde se define a classe Tarefa, que se materializará numa tabela, na base de dados, para armazenar tarefas.
1. Na aplicação admin (http://127.0.0.1:80007admin/) podemos editar tarefas (para aceder a admin deve criar um superuser `python manage.py createsuperuser`)
1. Na consola Python pode também manipular os dados da tabela com [queries](https://docs.djangoproject.com/en/3.2/topics/db/queries/). Veja [exemplo](#queries) em baixo de utilização.
1. no ficheiro `views.py` tem disponivel as 4 views existentes, que permitem listar (home_page_view), criar, editar e apagar tarefas
1. Os templates usam como base `base.html`, que está estilizado usando um CSS `base.css` assim como algumas classes do Bootstrap (estilos do Bootstrap são importados através de um link). Usam-se as classes jumbotron (para header) e btn para formatar botões.
2. na pasta `website\templates\pw` crie uma página HTML correspondente para ser renderizada, extendendo a base.html (veja como é feito nas outras páginas)
3. no ficheiro `website\urls.py` crie um novo `path` para o novo URL
4. no ficheiro `base.html` (que está na pasta `website\templates\website`) atualize o menu de navegação, inlcuindo um link para a nova página


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

## Queries

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
