# Aula #2 de Django: Web app Tarefas 

* Aplicação (o código está disponível neste GitHub) para gestão de tarefas, permitindo, criar, alterar e apagar tarefas, que usa um base de dados.
* Se quiser, veja um [vídeo](https://educast.fccn.pt/vod/clips/1m7vvfknq2/link_box_h?locale=en) da implementação desta aplicação, que tem capítulos para os vários tópicos abordados. 
* Este documento descreve os detalhes da arquitetura MVT no funcionamento da aplicação. Clique nos links para visualizar o código referenciada. 

## Primeiros passos para correr aplicação
1. Abra a linha de comandos (PowerShell ou cmd)
1. Descarregue uma cópia (clone) do repositório com o comando `git clone https://github.com/teoria-da-computacao/django-aula-02` 
1. Entre na pasta  `cd django-aula-02`
1. Garanta que tem o pipenv instalado, correndo o comando `python3 -m pip install pipenv`
1. Crie um ambiente virtual `pipenv install django` 
1. Active o ambiente virtual `pipenv shell`
1. Lance a aplicação no browser com o comando `python manage.py runserver`. 
1. Tem disponíveis as aplicações hello no link `http://127.0.0.1:8000`
1. abra a pasta com o Pycharm, para a explorar.
1. devera criar um superuser `python manage.py createsuperuser` para pode aceder ao modo admin e ditar diretamente a base de dados


# Models
1. A Web App Tarefas tem como ponto de partida o ficheiro [`models.py`](https://github.com/teoria-da-computacao/django-aula-02/blob/master/tarefas/models.py), onde a classe Tarefa especifica os atributos duma tarefa. Explore mais sobre este tópico [aqui](https://docs.djangoproject.com/en/3.2/topics/db/models/) 
2. Umavez criada a classe Tarefa, esta deve ser ativada. Sempre que criamos ou modificamos um modelo, devemos atualizar o Django com dois passos:
    * `python manage.py makemigrations tarefas`
    * `python manage.py migrate tarefas`
3. Esta operação cria uma tabela Tarefa na base de dados. 
3. Deve depois registar a aplicação em [`admin.py`](https://github.com/teoria-da-computacao/django-aula-02/blob/master/tarefas/admin.py)  
3. Deverá criar um superuser, com o comando `python manage.py createsuperuser` para pode aceder ao modo admin e editar diretamente a base de dados.
3. A aplicação admin é um interface integrado, acessível em http://127.0.0.1:8000/admin/, onde poderemos editar os elementos da tabela (criar, alterar, apagar tarefas) sem 'tocar' em código. 
4. Experimente criar novas tarefas e editar existentes.
7. As tarefas são instâncias da classe Tarefa, que ficam como registos da tabela Tarefa da base de dados.


# Manipulação da BD da consola Python
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

# Formulário
1. Em [`forms.py`](https://github.com/teoria-da-computacao/django-aula-02/blob/21a2f865f02eeb36007ac3e4916cc0dc69835c6b/tarefas/forms.py) é definida a classe TarefaForm, classe de formulário criada com base na classe Tarefa. É uma forma muito eficiente e simples para criar instâncias formulário. 
    * criar um objeto [formulário preenchido](https://github.com//teoria-da-computacao/django-aula-02/blob/master/tarefas/views.py#L17) com dados enviados pelo utilizador através de um template, que se válidos são guardados na base de dados.
    * 
2. É possível customizar os campos na classe Form:
    * podemos apresentar no formulário apenas um subset de atributos de Tarefa
    * [`labels`](https://github.com/teoria-da-computacao/django-aula-02/blob/21a2f865f02eeb36007ac3e4916cc0dc69835c6b/tarefas/forms.py#L18) a ser apresentadas em substituição do nome do atributo da classe 
    * [`widgets`](https://github.com/teoria-da-computacao/django-aula-02/21a2f865f02eeb36007ac3e4916cc0dc69835c6b/tarefas/forms.py#L11) permitem especificar pares propriedade=valor do elemento HTML `<input>` de um determinado campo do formulário; valores para propriedades tais como `class`, `placeholder`, valores `max` e `min`. 
    * [`help_texts`](https://github.com/teoria-da-computacao/django-aula-02/blob/21a2f865f02eeb36007ac3e4916cc0dc69835c6b/tarefas/forms.py#L25) especificam texto auxiliar dum determinado campo do formulário.


# Views e respetivos Templates para operações CRUD
1. Em [`views.py`](https://github.com/teoria-da-computacao/django-aula-02/blob/master/tarefas/views.py) existem 4 funções view que permitem listar, criar, editar e apagar tarefas (operações CRUD):
```Python 
home_page_view(request)
nova_tarefa_view(request)
edita_tarefa_view(request, tarefa_id)
apaga_tarefa_view(request, tarefa_id)
```
1. As views que renderizam templates com formulários têm duas partes:
    1. criam uma instância de formulário sem dados (`form = TarefaForm()`), inserido-o num template enviado ao utilizador para recolher dados.
    2. quando recebem dados (`request.POST`), guardam-nos num formulário (actualizado para `form = TarefaForm(request.POST or None)`), sendo estes então guardados na base de dados (`forms.save()`).
1. São detalhadas em baixo as views

## 1.1 View home_page_view() - simplificada
```python
def home_page_view(request):
    context = {'tarefas': Tarefa.objects.all()}
    return render(request, 'tarefas/home.html', context)
```
1. Lista as tarefas. Passamos no contexto o QuerySet de todos os objetos da base de dados obtido com `Tarefa.objects.all()`
2. poderiamos ordenar com a função sorted, em função da prioridade. com ```'tarefas': sorted(Tarefa.objects.all(), key=lambda objeto:objeto.prioridade, reverse=True)```

## 1.2 Template home.html - simplificado
```html
{% extends 'tarefas/base.html' %}

{% block main %}
    {% for tarefa in tarefas %}
        <p> 
           {{ tarefa.titulo }}
           <a href="{% url 'tarefas:edita' tarefa.id %}" class="btn btn-warning">Editar</a>
        </p>
   {% endfor %}
        <a href="{% url 'tarefas:nova' %}" class="btn btn-success btn-block">+ Nova Tarefa</a>
{% endblock %}
```
1. renderiza-se, com um ciclo for, incluindo apenas o título.
2. inclui-se para cada tarefa um botão que permite editar a tarefa. É passado no href, o id da tarefa, para que a view saiba qual tarefa deve ser editada.
3. O [template final](https://github.com/teoria-da-computacao/django-aula-02/blob/master/tarefas/templates/tarefas/home.html) fica mais colorido, pois é inserida mais informação (data, prioridade, concluido).

## 2.1 View nova_tarefa_view() - simplificada
```Python
def nova_tarefa_view(request):
    form = TarefaForm()
    context = {'form': form}
    return render(request, 'tarefas/nova.html', context)
```
1. Em [`nova_tarefa_view`](https://github.com/teoria-da-computacao/django-aula-02/blob/master/tarefas/views.py#L16), cria-se um formulário que irá vazio.
1. será enviado via dicionário context, para renderizar o template com o formulário de Tarefa.


## 2.2 Template nova.html
```html
{% extends 'tarefas/base.html' %}

{% block main %}
<h3>Nova tarefa</h3>
    <form action="" method="POST">
        {% csrf_token %}
        {{ form.as_p }}

        <a href="{% url 'tarefas:home' %}" class="btn btn-warning">Cancelar</a>
        <input type="submit" value="Gravar" class="btn btn-success" >
    </form>
{% endblock %}
```
3. Em template [`nova.html`](https://github.com/teoria-da-computacao/django-aula-02/blob/master/tarefas/templates/tarefas/nova.html#L8), no formulário, a variável {{ form.as_p }} inserirá todos os campos especificados em TarefaForm (*fields*). O sufixo `.as_p` indica para colocar cada input dentro de um elemento `<p>`. Se não se puser extensão, é coocado dentro de um elemento `<tr>`.
5. É necessário inseri o input submit.
6. É inserido um hiperlink cancelar para 'tarefas:home', caso queiramos cancelar a criação de nova tarefa. É estilizado como um botão com `class="btn"`. 
7. action="" quer dizer que o formulário é enviado de volta para a mesa view que o renderizou. Seria semelhante a especificar ```action="{% url 'tarefas:nova' %}"```

## 2.3 View nova_tarefa_view() - completa
```Python
def nova_tarefa_view(request):
    form = TarefaForm(request.POST or None)
    if form.is_valid():
        form.save()
        return HttpResponseRedirect(reverse('tarefas:home'))
    context = {'form': form}
    return render(request, 'tarefas/nova.html', context)
```
8. Quando é enviado de volta o formulário com dados, os dados são recebidos em `request.POST`. Cria-se um formulário preenchido com os dados de `request.POST`, especificando `form = TarefaForm(request.POST or None)` (o `None` é para o caso de não haver dados enviados). 
9.	caso o formulario seja válido:
    1. o comando form.save() grava diretamente os dados da nova tarefa na base de dados!
    1. depois, redireciona-se a aplicação para a view `tarefas:home`, com a lista de tarefas.
10. caso os dados sejam inválidos ou não tenha sido submetido nada, um formulario em branco será enviado novamente


## 3.1 View edita_tarefa_view
1. em [`home.html`](https://github.com/teoria-da-computacao/django-aula-02/blob/e784009df93d7ba80abe3ccb2c7d3b90ae55ee2e/tarefas/templates/tarefas/home.html#L23), para cada tarefa, é adicionado um hiperlink (transformado em botão com `class=btn`) `<a href="{% url 'tarefas:edita' tarefa.id %}" class="btn">`, identificando com tarefa.id a tarefa a editar
2. em `urls.py`, a [rota edita](https://github.com/teoria-da-computacao/django-aula-02/blob/e784009df93d7ba80abe3ccb2c7d3b90ae55ee2e/tarefas/urls.py#L11) especifica que recebe id da tarefa, um inteiro.
3. [`edita_tarefa_view`](https://github.com/teoria-da-computacao/django-aula-02/blob/master/tarefas/views.py#L27)  recebe como argumento, além do request, tarefa_id, primarykey da tarefa a editar.  
4. tarefa_id é usado para obter a respetiva [instância](https://github.com/teoria-da-computacao/django-aula-02/blob/baafbe87a25b47bdaf8708e977e8c5411496273b/tarefas/views.py#L28), permitindo criar um [formulário preenchido](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/views.py#L29) com os dados registados na tabela. 
5. além do formulário preenchido, é enviado no contexto o id da tarefa, usado na action do form em [`edita.html`](https://github.com/teoria-da-computacao/django-aula-02/blob/master/tarefas/templates/tarefas/edita.html), semelhante a nota.html, excepto que tem tarefa_id:
    `<form action="{% url 'tarefas:edita' tarefa_id %}" method="POST">`

## 3.2 Template edita.html
```html
{% extends 'tarefas/base.html' %}
{% block main %}
<h3>Tarefa</h3>
    <form action="{% url 'tarefas:edita' tarefa_id %}" method="POST">
        {% csrf_token %}
        {{ form.as_p }}

        <a href="{% url 'tarefas:home' %}" role="button" class="btn btn-warning">Cancelar</a>
        <input type="submit" value="Gravar" class="btn btn-success" >
        <a href="{% url 'tarefas:apaga' tarefa_id %}" role="button" class="btn btn-danger">Apagar</a>
    </form>
{% endblock %}
```
1. Template semelhante a nova.html, à excepção de `action="{% url 'tarefas:edita' tarefa_id %}"` que especifica tarefa_id, a tarefa a alterar.
2. Além do hiperlink/botão cancelar, é inserido um hiperlink/botão apagar, que mapeia para a view 'tarefas:apaga'

## 4.1 apaga_tarefa_view
```python
def apaga_tarefa_view(request, tarefa_id):
    Tarefa.objects.get(id=tarefa_id).delete()
    return HttpResponseRedirect(reverse('tarefas:home'))
 ```
1. esta view é chamada usando um botão apagar disponível em edita.html. 
1. recebe o id da tarefa a apagar, sendo usado para obter com o método `get()` a instância da tarefa, que é depois apagada com o método `delete()`
1. não renderiza nenhum template, redirecionando para a lista de tarefas, 'tarefas:home'.
2. alternativamente, pode-se criar um template apaga.html que pede para confirmar se se quer mesmo apagar a tarefa.


# URLs
1. O ficheiro `config/urls.py`, do projeto, é usado para encaminhar pedidos para a aplicação `tarefas`
2. O ficheiro `tarefas/urls.py` tem rotas que mapeiam os URLs para para as respetivas views:
```python
from django.urls import path
from . import views

app_name = 'tarefas'
urlpatterns = [
    path('',views.home_page_view, name='home'),
    path('nova/', views.nova_tarefa_view, name='nova'),
    path('edita/<int:tarefa_id>', views.edita_tarefa_view, name='edita'),
    path('apaga/<int:tarefa_id>', views.apaga_tarefa_view, name='apaga'),
]
```


# Templates
1. Os templates usam um layout base [`base.html`](https://github.com/teoria-da-computacao/django-aula-02/blob/master/tarefas/templates/tarefas/base.html), que é estendido por todos os templates da aplicação
1. O template base está estilizado por um CSS criado, [`base.css`](https://github.com/teoria-da-computacao/django-aula-02/blob/master/tarefas/static/tarefas/base.css). 
2. Também são usadas algumas classes Bootstrap (importadas através de um [link](https://github.com/ULHT-PW-2020-21/pw-aula-django-02/blob/master/tarefas/templates/tarefas/base.html#L7)). 
    * Classe jumbotron no [header](https://github.com/teoria-da-computacao/django-aula-02/blob/master/tarefas/templates/tarefas/base.html#L12)) 
    * Classe btn para formatar um botão, e associados btn-sucess, btn-warning, btn-warning que especificam cores (verde, amarelo e vermelho). Ver em [`nova.html`](https://github.com/teoria-da-computacao/django-aula-02/blob/master/tarefas/templates/tarefas/nova.html) e [`edita.html´](https://github.com/teoria-da-computacao/django-aula-02/blob/master/tarefas/templates/tarefas/edita.html#L9)).
