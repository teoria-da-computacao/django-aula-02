# Aula #2 de Django: Web app Tarefas 

* Aplicação para gestão de tarefas, permitindo, criar, alterar e apagar tarefas, que usa um base de dados.
* Construída na aula de 17.5.
* Este documento descreve os detalhes da arquitetura MVT no funcionamento da aplicação. Clique nos links para visualizar o código referenciada. 

## Primeiros passos para correr aplicação
1. Abra a linha de comandos (PowerShell ou cmd)
1. Descarregue uma cópia (clone) do repositório com o comando `git clone https://github.com/ULHT-PW-2020-21/pw-aula-django-02` 
1. Entre na pasta  `cd pw-aula-django-02`
1. Garanta que tem o pipenv instalado, correndo o comando `python3 -m pip install pipenv`
1. Crie um ambiente virtual `pipenv install django` 
1. Active o ambiente virtual `pipenv shell`
1. Lance a aplicação no browser com o comando `python manage.py runserver`. 
1. Tem disponíveis as aplicações hello no link `http://127.0.0.1:8000`
1. abra a pasta com o Pycharm, para a explorar.
1. devera criar um superuser `python manage.py createsuperuser` para pode aceder ao modo admin e ditar diretamente a base de dados


## Models
1. A Web App Tarefas tem como ponto de partida o ficheiro [`models.py`](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/models.py), onde a classe Tarefa especifica os atributos duma tarefa. Explore mais sobre este tópico [aqui](https://docs.djangoproject.com/en/3.2/topics/db/models/) 
2. Umavez criada a classe Tarefa, esta deve ser ativada. Sempre que criamos ou modificamos um modelo, devemos atualizar o Django com dois passos:
    * `python manage.py makemigrations tarefas`
    * `python manage.py migrate tarefas`
3. Esta operação cria uma tabela Tarefa na base de dados. 
3. Deve depois registar a aplicação em [`admin.py`](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/admin.py)  
3. Deverá criar um superuser, com o comando `python manage.py createsuperuser` para pode aceder ao modo admin e editar diretamente a base de dados.
3. A aplicação admin é um interface integrado, acessível em http://127.0.0.1:8000/admin/, onde poderemos editar os elementos da tabela (criar, alterar, apagar tarefas) sem 'tocar' em código. 
4. Experimente criar novas tarefas e editar existentes.
7. As tarefas são instâncias da classe Tarefa, que ficam como registos da tabela Tarefa da base de dados.


## Manipulação da BD da consola Python
1. uma vez criados os seus modelos de dados, o Django dá-lhe uatomaticamente uma API de abstração de dados que lhe permite criar, recuperar, atualizar e apagar objetos.
2. Para representar os dados da tabela da BD em objetos Python, Django usa um sistema intuitivo:
    * Uma classe de modelo representa uma tabela da BD
    * uma instância dessa classe representa um registro particular na tabela da BD.
    * QuerySet (coleção de objetos da base de dados)
    * mais detalhes [aqui](https://docs.djangoproject.com/en/3.2/topics/db/queries/) 
Na consola Python podemos importar a classe Tarefa e criar instâncias, guardá-las na base de dados, e depois pesquisá-las e manipulá-las (mais detalhes sobre *queries* encontra em [djangoproject](https://docs.djangoproject.com/en/3.2/topics/db/queries/)). 
2. Exemplos de manipulação:
```Python
from tarefas.models import Tarefa # importação da classe Tarefa, para manipular a BD

t1 = Tarefa(titulo='Ir correr', prioridade=2) # cria nova tarefa
t1.save()  # •	o Django não altera a BD até chamar explicitamente save(). corresponde à instrução SQL UPDATE
t2 = Tarefa(titulo='Ir ao cinema', prioridade=2) # cria nova tarefa
t2.save()
t3 = Tarefa(titulo='Fazer laboratório de PW', prioridade=2) # cria nova tarefa
t3.save()
t4 = Tarefa(titulo='Ir passear', prioridade=1); t4.save()  # cria nova tarefa

t3.prioridade = 3; t3.save() # alterar valor de atributo

tarefas = Tarefa.objects.all()   # retorna uma QuerySet, coleção de objetos da base de dados
t1.delete() # apaga t1 objeto da tabela
prioritarias = Tarefa.objects.filter(prioridade=1)  # Retorna um novo QuerySet dos objetos que correspondem aos kwargs de pesquisa fornecidos.
tarefa = Tarefa.objects.get(pk=1) # retorna instância específica
ordenadas = sorted(Tarefa.objects.all(), key=lambda objeto:objeto.prioridade)
```

## Formulário
1. Em [`forms.py`](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/21a2f865f02eeb36007ac3e4916cc0dc69835c6b/tarefas/forms.py) é definida a classe TarefaForm, classe de formulário criada com base na classe Tarefa. É uma forma muito eficiente e simples para criar instâncias formulário. 
    * criar um objeto [formulário preenchido](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/views.py#L17) com dados enviados pelo utilizador através de um template, que se válidos são guardados na base de dados.
    * criar um objeto [formulário preenchido](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/views.py#L29) com dados enviados pelo utilizador de uma determinada instância existente que, depois de válidados, são guardados na base de dados as alterações que recebidas.
2. É possível customizar os campos na classe Form:
    * podemos apresentar no formulário apenas um subset de atributos de Tarefa
    * [`labels`](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/21a2f865f02eeb36007ac3e4916cc0dc69835c6b/tarefas/forms.py#L18) a ser apresentadas em substituição do nome do atributo da classe 
    * [`widgets`](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/21a2f865f02eeb36007ac3e4916cc0dc69835c6b/tarefas/forms.py#L11) permitem especificar pares propriedade=valor do elemento HTML `<input>` de um determinado campo do formulário; valores para propriedades tais como `class`, `placeholder`, valores `max` e `min`. 
    * [`help_texts`](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/21a2f865f02eeb36007ac3e4916cc0dc69835c6b/tarefas/forms.py#L25) especificam texto auxiliar dum determinado campo do formulário.


## Views
1. Em [`views.py`](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/views.py) existem 4 funções view que permitem listar, criar, editar e apagar tarefas:
```Python 
home_page_view(request)
nova_tarefa_view(request)
edita_tarefa_view(request, tarefa_id)
apaga_tarefa_view(request, tarefa_id)
```
   
   * `home_page_view`: lista as tarefas. Passamos no contexto o QuerySet de todos os objetos da base de dados obtido com `Tarefa.objects.all()`


### `nova_tarefa_view`

1. Cria-se um [formulário vazio](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/views.py#L17) `form = TarefaForm()`, a inserir no template [`nova.html`](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/templates/tarefas/nova.html#L8).
1. uma vez submetido o formulário, devemos processar e armazenar os dados. para tal, insere-se `request.POST` no argumento, ficando `form = TarefaForm(request.POST or None)`, que caso tenham sido enviado dados por POST, preenche o form. 
1.	caso o formulario seja válido:
    1. o comando form.save() grava diretamente os dados da nova tarefa na base de dados!
    1. [redirecionamos](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/e784009df93d7ba80abe3ccb2c7d3b90ae55ee2e/tarefas/views.py#L20) para a view `tarefas:home`.
4. caso os dados sejam inválidos ou não tenha sido submetido nada, um formulario em branco será enviado novamente

### `edita_tarefa_view`
1. para cada tarefa, adicionamos um [botão para editar](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/e784009df93d7ba80abe3ccb2c7d3b90ae55ee2e/tarefas/templates/tarefas/home.html#L23) a tarefa, hiperlink com `class=btn`! Especifica-se em href a chave primaria tarefa.id, que servirá para identificar a tarefa a editar por edita_tarefa_view
2. em `urls.py`, a [rota edita](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/e784009df93d7ba80abe3ccb2c7d3b90ae55ee2e/tarefas/urls.py#L11) especifica que recebe id da tarefa, um inteiro.
3. `edita_tarefa_view` recebe como argumento, além do request, tarefa_id, primary-key da tarefa a editar.  
4. tarefa_id é usado para obter a respetiva instância, permitindo criar um formulário preenchido com os dados registados na tabela. 
5. [`edita.html`](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/templates/tarefas/edita.html) é semelhante a nota.html, excepto que tem:
    `<form action="{% url 'tarefas:edita' tarefa_id %}" method="POST">`



5. As views que renderizam templates com formulários têm duas partes:
    1. [criam uma instância de formulário](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/views.py#L17) sem dados, passada ao template para recolher dados dos utilizadores.
    2. guardam num formulário os dados submetidos, com `form = TarefaForm(request.POST)`, sendo osdados posteriormente [guardados na base de dados](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/views.py#L19).

### `apaga_tarefa_view`


## URLs
1. O ficheiro config/urls.py encaminha para a aplicação tarefas
2. O ficheiro tarefas/urls.py tem rotas para as views existentes


## Templates
1. Os templates usam como layout base [`base.html`](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/templates/tarefas/base.html), que é estendido por todos os templates da aplicação
1. O template base está estilizado por um CSS criado, [`base.css`](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/static/tarefas/base.css). 
2. Também são usadas algumas classes Bootstrap (importadas através de um [link](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/templates/tarefas/base.html#L7)). 
    * Classe jumbotron no [header](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/templates/tarefas/base.html#L12)) 
    * Classe btn para formatar um botão, e associados btn-sucess, btn-warning, btn-warning que especificam cores (verde, amarelo e vermelho). Ver em [`nova.html`](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/templates/tarefas/nova.html) e [`edita.html´](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/templates/tarefas/edita.html#L9)).
