# LiTOY : the List That Outlives You.

## What is LiTOY?
There are several ways to look at it :
* LiTOY is a python script using sqlite to create and manage a list of your goals, be it short, medium or long term but more importantly : it ranks them in a smart and very flexible way using pairwise comparisons and several [ELO scores](https://en.wikipedia.org/wiki/Elo_rating_system).
* An organizer aiming at centralizing all your goals in a single place while quickly ranking them in an order reflecting user preferences.
* A way for me to practice my Python (very rusty, still a long way to go, don't hesitate to do PRs or open issue, they will be greately appreciated).


The idea behing LiTOY is simple : 
1. have all items in the same sqlite database
2. automatically pick 2 items and prompt the user for which is better according to a user-specified question
3. adjust the ELO score of each item accordingly
4. When enough pairwise comparisons are done, rank the items according to a user-specified formula, for example the ranking could reflect `Books I want to read ordered by importance and by shortest time to read (i.e. "I want the most important short books first")`



## Examples of use :
### Managing a reading queue 
* in this example : the items could only contain : URL ; details
* the user will only have to compare the importance of each article/book, the reading time will be automatically retrieved from the url
* the pairwise comparison of the reading time will correspond to the question "Which is shorter to read" and will be done automatically without user input.
* The final ranking can be something like `reading_time_ELO + importance_ELO`
* This should help the user read in order of what is most important AND shorter to read.


### Managing a movie Watchlist
* in this example : the items could only contain : a movie file path
* the code will, all by itself, retrieve the duration of the movie, the size of the file
* the user could be prompted with the question : "Which movie is the most important for you?", the answer will impact the respective ELO scores of the items
* The final ranking could be a weighted sum : `importance_ELO + duration_ELO + 1.2*size_ELO`
* This should allow the user to watch movies that are taking the most space but are also important while not lasting 4 hours.

### Managing a list where tasks have to be done in a specific order (example : errands, diy builiding)
* in this example : the items could only contain : a goal in text format
* The user could be prompted with the question : "Which task should be done first?", the answer will change the ELO score of each items
* The final ranking should quickly converge towards the correct order for steps. Probably works for finding the shorted path if you have errands too!



## FAQ
**Where does the idea come from?** From Gwern's [media resorted](https://www.gwern.net/Resorter).

**Do you have any idea it will work or at least converge towards something useful without doing thousands of fights a day?** Lol no.

**What does LiTOY stand for?** It's about the List that Outlives You. It is used as a [memento mori](https://en.wikipedia.org/wiki/Memento_mori)


**Do you accept criticism and/or contribution?** Hell Yeah! All help and criticisms are welcome.

**What are ELO scores? Why did you choose this algorithm?** A ranking system initially devised for chess. The idea is that if you have chess players A, B and C : if `A beats B` and `B beats C` then you don't really have to organize a fight between A and C to know which is better. It does so by assigning a score to each oponnent that can then be used to compare opponents that have never met each other. A strength of ELO is that it still behaves well even if some players underperform (or overperform). In the case of LiTOY, you can have some wrong comparisons in your db and it will not throw off the whole ranking. Also, ELO is dead easy to implement and I wanted to have a complete understanding of my code.

**What platform does it run on?** Try to make it as agnostic as possible but I'm on Linux and I might occasionnaly use unix only exec without paying attention. Don't hesitate to tell me if you run into an issue.

** What is manual mode ?** It launches python in a special way that allows yourself to launch script functions directly, very useful for debugging as well as undoing stuffo

**How can I undo X?** It is not really possible for now. But you can access the logs and see what you did wrong. Hopefully this can help you repair damage. Rollback features might be added sometime in the future. If you have any issue feel free to open one, especially if you think your action was not recorded in the log.

**What are answer level number ?** If you answer 1 it means you favor the entry on the left compared to the one on the right. 5 means you favor the right one. 3 is obviously the middle gounrd but is not the same as skipping the fight. Of course, all this is relative to the question that is being considered.



## How can I use this?
* Read this page thoroughly. Don't be afraid to ask questions.
* `git clone https://github.com/thiswillbeyourgithub/LiTOY/ `
* `cd LiTOY`
* edit the settings in `settings.py`
* `python3 ./__main__.py`

### Syntax example :
* adds a new entry to deck todo with the tag diy  `python3 __main__.py --add 'repair the tires' diy 'to do before march'`
* shows the rank `python3 __main__.py --rank --deck="diy" -n 20`
* shows all entries `python3 __main__.py --rank --deck="*"`
* shows all entries, by date added (can be any other sql field), in reverse order `python3 __main__.py --list="date_added rev`
* show all entries, unformatted `python3 __main__.py --list="raw"`
* get the field list `python3 __main__.py --print--field`
* compare 10 cards in a row from the folder called diy `python3 __main__.py --compare --deck="diy" -n 10`
* get history `python3 __main__.py --history`
* turn on manual mode `python3 __main__.py --manual`
* check database (do this after major import) `python3 __main__.py --check-db`
* open in sqlite browser : `python3 __main__.py --external`
* also show output to the console instead of just in the debug file : `--verbose`
* edit FIELD from card with id ID : `python3 __main__.py --edit ID FIELD VALUE`

### Features 
* Automatically retrieves video length, video size, article reading time duration, pdf reading time duration
* Designed with flexibility in mind. You just have to specify a question.

## Code explanation
### Settings
### Data structure of the db

## TODO :
* recuperer unen entree sous forme de dictionnaire puis le reintroduire, ca a l'air d'etre BY FAR le plus simple a maintenir
* rajouter un champs type (text / video / audio / manual) 
        * le time to read doit etre time to watch si il s'agit d'une video, utiliser youtube-dl pour trouver la durée si url, ou ffmpeg si c'est un fichier local
        * si c'est un pdf : utiliser pdftotext puis le code pour estimer le temps pour lire une page web
* don't pick if card disabled
* renommer tous les elo en elo1 et elo2
    * renommer les mode en elo du coup, et faire une equivalence dans settings
        * uniformiser le nom des delta pour que ca soit delta1 etc
* faire un requirement file et le mentionner dans le how to use
* prendre le temps de reflechir et repondre a ce message https://www.lesswrong.com/posts/54Bw7Yxouzdg5KxsF/how-do-you-organise-your-reading
* rajouter une syntaxe pour importer avec directement des infos : __length=37pages   __length=17minutes __deck=audio etc et surtout __1/2/3/4 indique ____ si on lui donne par défaut le score du premier 2e 3e ou 4e cinquieme du classement
* il faut qu'un raccourci permette d'ouvrir automatiquement les url dans un navigateur + que ca soit configurable
* utiliser ce module pour gerer les settings : https://pypi.org/project/simple-settings/
* organiser un autre fichier contenant la todo list, dont une section "long term ideas", ex :
* il y a aucun moyen que ce soft decolle si il est pas commenté abusivement, pareil avec check le pep8 de vim sur ces fichiers, ca prend pas tres longtemps
* check db should happen periodically and after every import
        * penser a rajouter un mode "court terme" qui permet simplement de trier des trucs selon lequel doit etre fait en premier
* rewrite the long SQL request so that it's on multiple lines
* verifier par le calcul que l'ordre dans lequel on fait les combats n'a pas d'importance, ca conditionne la maniere dont tu stockes les resultats
* --list should be the same as --rank but rank shouldn't show disabled
* champs "estimated time to read" + "title of url"
        * faut que ca marche aussi si plusieurs url sont données
* verifier que chaque message important est dans le log
* rajouter un message d'erreur quand il y a aucune carte et que le mec a rien importé
* si importé a partir d'un fichier : bouger le fichier dans un dossier ./imported/+date
* aléatoirement rajouté un message TU VAS MOURIR sometimes
* les tupple sont plus rapides que les listes, les priviléger
* dans l'url : au cas ou : changer ` en '    ' `
* il faut en fait rajouter genre 5 fois des fields qui soit score1/2/3/4/5, score_name, score_question et l'utiliser pour faire des trucs plus cmoplexes genre noter des films rapidement, car prendre en compte la taille de fichier video serait ouf
* il faut rajouter une valeur "average" dans les scores qui serait automatiquement transformée en la moyenne des scores correspondant, par exemple pour si tu as un dvd dans une liste de fichier films, tu auras pas la taille du dvd mais tu auras sa longueur et son importance donc ca ferait foirer la formule
* function that automatically checks fields consistency :
        * estimated time to read
        * title of url
        * delta
        * presence of importance score
        * check for duplicates in url
        * presence of time score
        * nb of time of importance score is the same as nb of time score
        * same for importance
* keep log size < 5MB or compress it
* faire des simulations avec une fausse liste d'item du genre [nombre de 1 a 100 ; nombrer random de 1 a 100]
        * utiliser ca pour avoir une idée de la vitesse de convergence
            * utiliser plusieurs valeurs de K
                * tester avec un truc genre "90% de chance de donner la bonne réponse" pour voir comment les real worls data diffèrent des stats theoriques
* use different color for the prompt and the display, to make it more readable and overall nicer to read
* idée : en fait on peut avoir un score par question, et permettre de rajouter des question tout seul non ? voir a la longue si ca serait reelement utile
* if url is found : compute estimated time to read
    * if url not found : use waybackmachine
* racourci qui ouvre sqlite browser
* fonction qui choppe/stocke une/plusieurs valeur de la db
*   fixer les valeurs initiales de la db
    * demander si on veut d'emblées rajouter une categorie lors de l'importation en affichant les categories deja existantes
* tirage au sort du plus grand differentiel
* pour corriger un field : https://stackoverflow.com/questions/2533120/show-default-value-for-editing-on-python-input-possible/2533142#2533142
* calcul de la moyenne  des differentiels
* auto backup a chaque changement de details/deck/title etc
* permettre d'exporter la liste or something, eventuellement en format ical, ou format anki
* idée de flo : exporter vers maniana (en tout cas il faut jeter un oeil pour voir ce que c'est) https://f-droid.org/fr/packages/com.zapta.apps.maniana/ 
* il faut que ca affiche le nombre de truc a faire qui sont todo, et si ca croit ou decroit sur 7 jours
* pour la cli : ce truc a l'air cool pour afficher le tables des podiums : http://zetcode.com/python/prettytable/
* pour l'interface une fois que tu as verifie que ca marche bien en cli : apprendre pyqt, clairement ce sera le plus simple et ca t'ouver la porte vers completer anki
* regarder si il y a pas un moyen plus simple que de faire une backup, genre un moyen de faire des unfo a l'infini ou de garder un historique
* rajouter un champs winAgainst et loseAainst, on rajoute l'id de l'adversaire dans le field a chaque combat, ca semble utile pour "refaire" des tris" apres coups ; pas encore sur d'a quoi ca sert mais il faut compter les draws aussi
* idée d'antoine pour l'interface : permettre des command line command genre : litoy set ID --deck "truc" ou litoy list --time ou litoy fight -n=50  ou litoy import file  ou   litoy backup    ou    litoy status   ou    litoy testrun 
* noter quelque part que si litoy marche bien il faudra le compatibiliser avec Polar, soit le rendre ineractif (genre qu'il recup des data de polar) soit carrement en le recodant a l'interieur

### formerly in the TODO but still thinking about it 
* 

