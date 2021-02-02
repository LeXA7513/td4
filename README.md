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

Dans le fichier main ajouter une nouvelle fonction

```python
from bottle import template

@app.route('/hello/<name>')
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
liste de chiffres afficher le résultat.

- Faire une saisie sur un champ, les chiffres sont à séparer par des ';'
- convertir la chaine en liste avec la fonction split()
  `ex : '1,2'.split(',') == ['1', '2'] == True`
- convertir la liste en flottants
- effectuer et renvoyer le calcul

Pour sauter une ligne utiliser la balise `<br/>` exemple()


4/ ajouter un second champ qui permet de choisir quelle fonction
statistique on souhaite utiliser.

- creer un dictionnaire qui contient `{'nomdela fonction': fonctio_definie_ou_importee}`



5/ tests/deverminage

Tester votre application en ajoutant le code suivant dans un fichier test_site.py

```
from webtest import TestApp
from server import app

testapp = TestApp(app)


def test_1():
    index = testapp.get("/")
    assert "Bonjour" in index.ubody


def test_formulaire():
    formulaire = testapp.get("/formulaire")
    formulaire.form["parametre1"] = "32"
    res = formulaire.form.submit()
    assert True  # A remplacer et completer

```
lancer les tests avec la commande `pytest -s .`
en cas de doute à une ligne vous pouvez utiliser `import ipdb; ipdb.set_trace()`.

Cela vous ouvrira le debugger dans le même contexte que celui ou le code s'execute,
une fois terminé appuyez sur c ou q puis entrée pour respectivement continuer ou quitter.


6/Ajouter le cas ou vous souhaitez effectuer tous les calculs.

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

 Attention: ne cherchez pas a faire le calcul a l'interieur du gabarit.
 pour pouvoir utilier % et boucler il faut que tous les autres caracteres preceent soient des espaces.



