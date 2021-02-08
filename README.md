Serveur web
===========

Créer un nouveau repl.

Créer un fichier server.py contenant

```python


from bottle import Bottle, run

app = Bottle()

@app.route('/')
def bonjour():
    return "Bonjour !"
```

Dans main.py

```python

from bottle import run
from server import app

run(app, host='0.0.0.0', port=8080, debug=True, reloader=True)

```

Lancer, vérifier qu'une fenêtre s'ouvre bien avec un navigateur.
Au besoin ouvrir dans un nouvel onglet (en haut à droite du
navigateur intégré)

1/Ajouter un paramètre simple

Dans le fichier server ajouter une nouvelle fonction

```python
from bottle import template

@app.route('/hello/<name>') #Ici on passe le parametre `name` dans l'url
def greet(name='Stranger'):
    return template('Hello {{name}}, how are you?', name=name)

```

Ouvrir dans un onglet à part, ajouter a la fin de l'url: hello/XXX

2/premier formulaire
2.a/ créer le premier formulaire

```python

@app.get("/formulaire")
def afficher_formulaire():
    return """
        <form action="/formulaire" method="post">
            Texte1 <input name="parametre1" type="text" />
            <input value="Ajouter" type="submit" />
        </form>
    """
```

2.b/ traiter les données soumises

```python

from bottle import request

@app.post("/formulaire")
def traiter_formulaire():
    valeur = request.forms.get("parametre1")
    return valeur

```


3/créer un formulaire en s'inspirant du point 2 pour saisir dans le champ une
liste de chiffres afficher la somme des chiffres saisis.

- Faire une saisie sur un champ, les chiffres sont à séparer par des ';'
- convertir la chaine en liste avec la fonction split()
  ex `'1,2'.split(',')` vaut `['1', '2']`
- convertir la liste en liste de flottants
- effectuer et renvoyer le calcul

Pour sauter une ligne utiliser la balise `<br/>` exemple()


4/ ajouter un second champ qui permet de choisir quelle fonction
statistique on souhaite utiliser.

- creer un dictionnaire qui contient `{'nomdela fonction': fonctio_definie_ou_importee}`



5/ tests/deverminage

Tester votre application en ajoutant le code suivant dans un fichier test_site.py

``` python
from webtest import TestApp as make_app
from server import app

app.catchall = False

testapp = make_app(app)

def test_1():
    index = testapp.get("/")
    assert "Bonjour" in index.ubody


def test_double():
    formulaire = testapp.get("/doubler")
    form = formulaire.form
    form["valeur"] = "32"
    res = form.submit()
    assert "64" in res.ubody  
```

Ajouter dans server.py

``` python

@app.get("/doubler")
def calcul():
    return """
     <form action="/doubler" method="post">
            valeur <input name="valeur" type="text" />
        <input value="Ajouter" type="submit" />
        </form>
    """


@app.post("/doubler")
def doubler_valeur():
    data = request.forms
    valeur = int(data.get("valeur"))
    double = valeur * 2
    res = {"valeur": valeur, "double": double}
    return template("{{valeur}} * 2 = <br/> {{double}}", valeur=valeur, double=double)


```
lancer les tests avec la commande `pytest -s .`
en cas de doute à une ligne vous pouvez utiliser `import ipdb; ipdb.set_trace()`.

Cela vous ouvrira le debugger dans le même contexte que celui ou le code s'execute,
les principales commandes sont : 
    - n pour passer a la ligne suivante
    - c pour continuer
    - q pour quitter
    - liste le code




6/ Étoffer vos tests utilisation de json

Plutot que de renvoyer une page et remplir des formulaires
 on aimerai pouvoir manipuler des objets qui ressemblent 
 un peu plus a des dictionnaires.


```python 
@app.post('/doubler.json')
def doubler_valeur_json():
    data = request.json 
    valeur = int(data.get("valeur"))
    res = {'double': valeur*2}
    return res

```

```python
def test_double_json():
    reponse = testapp.post_json("/doubler.json", {"valeur": 43} )

    assert reponse.json["double"] == 86
```

7/ Fusionner les deux approches et adapter le contenu en fonction inspirez vous de l'exemple suivant

``` python
app.post("/???")
def traiter():
   
    is_json = request.content_type == "application/json"
    data = request.json if is_json else request.forms

    #vos caluls ici

    res = {} # a remplir
    
    if is_json:
        return res
    else:
        return template("{{clef_xx}} = {{clef_yy}}", **res)


```

8/ Ajouter le cas ou vous souhaitez effectuer tous les calculs.

Un exemple minimal:

```python
@app.get("/demo_template")
def demo_template():
    items = list(zip("abc", "123"))
    tmpl = """
    <ul>
     % for key, value in items:
        <li>{{key}}: {{ value }}</li>
    % end
    </ul>
    """
    return template(tmpl, items=items)

Attention: ne cherchez pas a faire le calcul a l´interieur du gabarit. 
 pour pouvoir utilier % et boucler il faut que tous les autres caracteres précedents 
 soient des espaces.


