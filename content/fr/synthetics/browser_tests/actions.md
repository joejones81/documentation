---
description: Enregistrer les étapes d'un test Browser Synthetic
further_reading:
- link: /synthetics/browser_tests/advanced_options/
  tag: Documentation
  text: Apprendre à configurer les options avancées de votre test Browser
- link: https://registry.terraform.io/providers/DataDog/datadog/latest/docs/resources/synthetics_global_variable
  tag: Terraform
  text: Créer et gérer des variables globales Synthetic avec Terraform
kind: documentation
title: Étapes des tests Browser
---

## Présentation

Les étapes correspondent à une série d'actions enregistrées pour créer un test Browser, que vous pouvez ensuite modifier ou enrichir. Les étapes que votre test Browser doit exécuter peuvent être définies soit automatiquement à l'aide de l'extension d'enregistrement de test Datadog, soit manuellement en ajoutant vous-même l'étape de votre choix. Toutes les étapes proposent des [options avancées][1] que vous pouvez configurer.

**Remarque** : le délai d'expiration par défaut pour chaque étape est d'environ 60 secondes. Vous pouvez modifier ce délai via l'[option avancée][2] dédiée.

## Étapes enregistrées automatiquement

Les étapes suivantes sont enregistrées automatiquement via l'[extension d'enregistrement de test Browser Datadog][3] :

### Click

[L'extension d'enregistrement de test Browser Datadog][3] peut automatiquement enregistrer les clics sur votre page. Indiquez le type de clic que vous souhaitez que votre test Browser effectue lors de son exécution :

{{< img src="synthetics/browser_tests/browser_test_click_step.mp4" alt="Étape Click d'un test Browser" video="true" width="60%">}}

Choisissez parmi les options suivantes :

* Primary click (correspond à un clic gauche)
* Double click
* Contextual click (correspond à un clic droit)

### Type text

[L'extension d'enregistrement de test Browser Datadog][3] peut automatiquement enregistrer la saisie de texte dans n'importe quel champ de votre site (formulaire, zone de texte, etc.) :

{{< img src="synthetics/browser_tests/input_text.mp4" alt="Étape Type text d'un test Browser" video="true" width="100%">}}

### Select option

[L'extension d'enregistrement de test Browser Datadog][3] peut automatiquement enregistrer la sélection d'options depuis le menu déroulant `select` :

{{< img src="synthetics/browser_tests/select_options.png" alt="Étape Select option" style="width:60%;">}}

### Upload file

Vous pouvez enregistrer l'importation de fichiers en tant qu'étape. Pour enregistrer une étape **Upload**, vous disposez de deux options :

* ouvrez votre bureau depuis votre navigateur ; ou
* faites glisser votre fichier dans l'iframe d'enregistrement.

{{< img src="synthetics/browser_tests/upload_file_step.png" alt="Créer une étape Upload file" style="width:60%;">}}

Une limite de 10 fichiers (et de 5 Mo par fichier) s'applique. L'étape Upload est prise en charge par les tests Browser exécutés depuis des emplacements gérés.

## Étapes ajoutées manuellement

Les étapes suivantes peuvent être ajoutées manuellement à un test Browser en les configurant depuis la page d'enregistrement d'un test Browser :

### Assertion

{{< img src="synthetics/browser_tests/browser_test_assertions.png" alt="Assertion de test Browser" style="width:60%;">}}

Les assertions vous permettent de vérifier que votre test Browser se déroule correctement à n'importe quelle étape du parcours utilisateur simulé. C'est la raison pour laquelle vos tests Browser doivent se terminer par une **Assertion** afin de confirmer leur bonne exécution.

Certaines assertions sont exécutées sur la **page active**. La page active désigne la page sur laquelle a été effectuée la dernière interaction, telle qu'un **Click** ou une **Assertion** sur un élément spécifique.

#### Test an element's content

Sélectionne un élément et vérifie s'il contient une valeur spécifique. Il est par exemple possible de sélectionner un `div` et de vérifier s'il contient ou non le mot « hello ».

#### Test an element's attribute

Sélectionne un élément de votre page et vérifie si l'un de ses attributs correspond au contenu attendu. Par exemple, il est possible de vérifier que la valeur d'un attribut `src` correspond au chemin de l'image attendue.

#### Test that some text is present on the active page

Vérifie qu'un texte spécifique est présent sur la page actuelle.

#### Test that some text is not present on the active page

Vérifie qu'une chaîne de texte spécifique est **ABSENTE** de la page actuelle.

#### Test the content of the URL of the active page

Récupère l'URL de la dernière page utilisée, puis vérifie si une valeur spécifique (`string`, `number`, `regex`) est présente dans l'URL.

#### Test that an element is present on the active page

Vérifie qu'un élément spécifique (`span`, `div`, `h`, `a`, etc.) est présent sur la page actuelle.

#### Test that an email was received

Vérifie qu'un e-mail a été envoyé et si certaines valeurs spécifiques (`string`, `number`, `regex`) sont présentes dans l'objet ou le corps de cet e-mail. Cette assertion a recours aux [variables d'e-mail][4] : vous devrez donc créer une variable d'e-mail avant de pouvoir exécuter une assertion `Test that an email was received`.

#### Test your UI with custom JavaScript

Testez une assertion personnalisée sur la page active en utilisant votre propre code JavaScript.

**Remarque** : les assertions JavaScript prennent en charge le code synchrone et asynchrone.

La fonction d'assertion JavaScript accepte les paramètres suivants et nécessite une instruction return.

* L'instruction `return` (obligatoire) doit correspondre à la condition que l'assertion doit remplir pour que l'étape de votre test Browser réussisse. N'importe quel type peut être renvoyé, mais la valeur est automatiquement convertie en booléen.

* `vars` (facultatif) : une chaîne contenant les [variables][5] de votre test Browser. Utilisez `vars.<VOTRE_VARIABLE>` pour appeler une variable de test Browser dans votre snippet JavaScript. Par exemple, si votre test Browser contient une variable `USERNAME`, appelez-la dans votre snippet JavaScript avec `vars.USERNAME`.

* `element` (facultatif) : l'emplacement de l'élément sur la page. Pour configurer ce paramètre, utilisez les boutons **Select** et **Update** sur l'élément cible. L'élément sélectionné tire automatiquement parti de l'algorithme de localisation multiple des tests Browser Datadog.

{{< img src="synthetics/browser_tests/js_assertion.mp4" alt="Assertion JavaScript dans un test Browser" video="true" width="100%">}}

Étant donné que les assertions JavaScript s'exécutent dans le contexte de la page active, ces étapes peuvent également accéder à tous les objets définis dans la page active (bibliothèques, builtins, variables globales, etc.). Pour charger des bibliothèques externes, utilisez une promise. Par exemple :

```javascript
const script = document.createElement('script');
script.type = 'text/javascript';
script.src = "https://code.jquery.com/jquery-3.5.1.slim.min.js";
const promise = new Promise((r) => script.onload = r)
document.head.appendChild(script)

await promise

// Le script est maintenant chargé

return jQuery().jquery.startsWith('3.5.1')
```

**Remarque** : les tests Browser chargent un fichier JavaScript externe en l'ajoutant à la page. Pour que l'étape de chargement fonctionne, il faut donc que votre site Web accepte le fichier.

#### Test a downloaded file

Effectuez des vérifications sur un fichier téléchargé lors des étapes précédentes. Il est possible de vérifier qu'un fichier a bien été téléchargé et d'exécuter une assertion sur son nom, sa taille et sa valeur MD5.

**Remarque** : pour mieux comprendre comment tester des fichiers téléchargés, consultez [ce guide dédié][6].

### Navigation

{{< img src="synthetics/browser_tests/navigation_step.png" alt="Étape Navigation d'un test Browser" style="width:60%;">}}

#### Refresh this page

Actualiser la page actuelle du scénario.

#### Go to email and click link

Cette étape vous permet d'accéder à des boîtes de messagerie Synthetic uniques après avoir créé une [variable d'e-mail][4]. Choisissez un e-mail et cliquez sur le lien sur lequel le test Browser doit cliquer. Votre test accédera alors à la page correspondante, à partir de laquelle vous pourrez continuer le parcours utilisateur.

#### Follow this link

Accéder à une page spécifique.

**Remarque** : vos URL doivent être précédées de `http` ou `https` dans le champ **Enter link URL**.

### Actions spéciales

[L'extension d'enregistrement de test Browser Datadog][3] permet d'enregistrer la plupart des étapes des parcours utilisateur que vous souhaitez surveiller. Toutefois, certaines étapes telles que **Hover**, **Press Key** et **Scroll** ne sont pas enregistrées automatiquement. Ajoutez-les manuellement à l'aide du menu **Special Actions** situé en haut à gauche de l'enregistreur.

#### Hover

Pour éviter de générer une étape à chaque fois que vous passez votre curseur sur un élément durant l'enregistrement, l'ajout de ce type d'étape se fait via une étape dédiée et un clic.

Après avoir sélectionné l'étape **Hover**, cliquez sur l'élément que vous souhaitez sélectionner pour créer une étape.

#### Press key

Vous pouvez simuler la sélection de touches par un utilisateur à l'aide d'étapes **Press Key**. Les touches suivantes peuvent être enregistrées à l'aide de l'[extension d'enregistrement de test Browser Datadog][3] :

* Entrée
* Touches fléchées (haut, bas, droite et gauche)
* Tabulation (en dehors d'un formulaire)
* Échap 
* Retour arrière

Pour appuyer sur des touches qui ne sont pas enregistrées automatiquement, indiquez les valeurs des touches qui doivent être sélectionnées dans le champ Value de l'étape spéciale **Press Key** :

{{< img src="synthetics/browser_tests/browser_test_press_key.png" alt="Action Press Key d'un test Browser" style="width:60%;">}}

Les modificateurs ci-dessous peuvent également être appliqués à la valeur saisie :

* Alt
* Control
* Meta
* Shift

#### Scroll

Les tests Browser font automatiquement défiler la page jusqu'à l'élément avec lequel ils doivent interagir. Par conséquent, dans la plupart des cas, il n'est pas nécessaire d'ajouter manuellement une étape de défilement. L'étape de défilement doit être ajoutée uniquement lorsque celle-ci est nécessaire pour déclencher une requête réseau supplémentaire, comme dans un défilement infini.

{{< img src="synthetics/browser_tests/browser_test_scroll_step.png" alt="Étape Scroll d'un test Browser" style="width:60%;">}}

Vous devez indiquer le nombre de pixels que votre test Browser doit faire défiler dans le sens horizontal ou vertical.

Par défaut, l'étape **Scroll** fait défiler la page entière. Si vous souhaitez que votre test Browser fasse défiler un élément spécifique (tel qu'un `<div>` spécifique), utilisez l'option **Target Element** pour le sélectionner.

#### Wait

Par défaut, Datadog attend que la page soit complètement chargée avant d'effectuer une étape ou de passer à l'étape suivante, avec un délai d'expiration de 60 secondes. Si vous savez que le chargement d'une page ou d'un élément d'une page prend plus de 60 secondes, vous pouvez personnaliser ce délai d'expiration dans les [options avancées][2] de votre étape ou ajouter une étape Wait codée en dur (valeur maximale de 300 secondes).

**Remarque** : le temps supplémentaire est systématiquement rajouté à **chaque exécution** du scénario de votre test Browser.

### Variable

{{< img src="synthetics/browser_tests/variables.png" alt="Variables d'un test Browser" style="width:60%;">}}

Pour créer une variable, donnez-lui d’abord un nom, puis définissez sa valeur en utilisant l'une des méthodes ci-dessous. Pour découvrir comment utiliser des variables au sein de vos étapes, consultez la rubrique [Utilisation des variables](#utilisation-des-variables).

#### Pattern

Créez une variable en définissant sa valeur sur l'un des builtins disponibles ci-dessous :

`{{ numeric(n) }}`
: Génère une chaîne numérique de `n` chiffres.

`{{ alphabetic(n) }}`
: Génère une chaîne alphabétique de `n` lettres.

`{{ alphanumeric(n) }}`
: Génère une chaîne alphanumérique de `n` caractères.

`{{ date(n, format) }}`
: Génère une date dans l'un des formats acceptés. Sa valeur correspond à la date d'initiation du test + `n` jours.

`{{ timestamp(n, unit) }}` 
: Génère un timestamp dans l'une des unités acceptées. Sa valeur correspond au timestamp d'initiation du test +/-  `n` unités choisies.

#### Élément

Créez une variable à partir d'un `span`, d'un `div` ou d'un autre élément en extrayant le texte de cet élément.

#### JavaScript

Rédigez le code JavaScript personnalisé qui renvoie la valeur à laquelle vous souhaitez que votre variable soit assignée.

**Remarque** : les étapes JavaScript prennent en charge le code synchrone et asynchrone.

La fonction JavaScript accepte les paramètres suivants et nécessite une instruction return.

* L'instruction `return` (obligatoire) doit renvoyer la valeur que vous souhaitez associer à votre variable JavaScript. N'importe quel type peut être renvoyé, mais la valeur est automatiquement convertie en chaîne.

* `vars` (facultatif) : une chaîne contenant les [variables][5] de votre test Browser dont vous souhaitez vous servir dans votre code. Utilisez `vars.<VOTRE_VARIABLE>` pour appeler une variable de test Browser dans votre snippet JavaScript. Par exemple, si votre test Browser contient déjà une variable `PRICE`, appelez-la dans votre snippet JavaScript avec `vars.PRICE`.

* `element` (facultatif) : l'emplacement de l'élément sur la page. Pour configurer ce paramètre, utilisez les boutons **Select** et **Update** sur l'élément cible. L'élément sélectionné tire automatiquement parti de l'algorithme de localisation multiple des tests Browser Datadog.

{{< img src="synthetics/browser_tests/js_variable.mp4" alt="Variable JavaScript de test Browser" video="true" width="100%">}}

Étant donné que les assertions JavaScript s'exécutent dans le contexte de la page active, ces étapes peuvent également accéder à tous les objets définis dans la page active (bibliothèques, builtins, variables globales, etc.). Pour charger des bibliothèques externes, utilisez une promesse. Par exemple :

```javascript
const script = document.createElement('script');
script.type = 'text/javascript';
script.src = "https://code.jquery.com/jquery-3.5.1.slim.min.js";
const promise = new Promise((r) => script.onload = r)
document.head.appendChild(script)

await promise

// Le script est maintenant chargé

return jQuery().jquery.startsWith('3.5.1')
```

**Remarque** : les tests Browser chargent un fichier JavaScript externe en l'ajoutant à la page. Pour que l'étape de chargement fonctionne, il faut donc que votre site Web accepte le fichier.

#### Variable globale

Choisissez n’importe quelle variable globale définie à l’aide des [paramètres de surveillance Synthetic][7].

#### Variable globale – Authentification multifacteur (MFA)

Choisissez n'importe quelle variable globale MFA définie à l’aide des [paramètres de surveillance Synthetic][7].

Ce type de variable globale conserve les clés secrètes de mots de passe à usage unique basés sur le temps (TOTP), ce qui vous permet de tester vos modules MFA et vos workflows protégés par MFA.

Pour en savoir plus sur l'authentification multifacteur basée sur un TOTP dans un test Browser, consultez le [guide TOTP][8].

#### E-mail

Générez une adresse e-mail Synthetic aléatoire et utilisez-la dans vos étapes de test pour [vérifier si un e-mail a bien été envoyé][9] ou pour [accéder à un lien présent dans l'e-mail][10] (par exemple, en cliquant sur un lien de confirmation). Une boîte de réception unique est générée à chaque exécution du test pour éviter tout conflit d'une exécution à l'autre.

### Sous-tests

{{< img src="synthetics/browser_tests/subtest.png" alt="Sous-test d'un test Browser" style="width:60%;">}}

Vous pouvez exécuter des tests Browser dans d'autres tests Browser afin de réutiliser des workflows existants (jusqu'à deux niveaux d'imbrication). Découvrez pourquoi nous vous conseillons d'utiliser des sous-tests et obtenez des exemples en consultant [ce guide dédié][11].

Les variables des sous-tests peuvent être ignorées dans les tests parents. Pour ce faire, vous devez vous assurer que les variables créées au niveau du test parent portent le même nom que celles présentes dans le sous-test. Par défaut, le sous-test est exécuté à la suite des étapes précédentes du test parent, mais ce comportement peut être modifié depuis les [**options avancées du sous-test**][12].

**Remarque** : s'il n'est pas pertinent d'exécuter un sous-test de façon indépendante dans votre scénario, vous avez la possibilité de le mettre en pause. Il recevra toujours des appels dans le cadre du test principal, mais ne sera pas exécuté de façon individuelle.

### Requêtes HTTP

Vous pouvez exécuter des requêtes HTTP au sein de vos tests Browser.

{{< img src="synthetics/browser_tests/recorder_http_requests2.png" alt="Étape requête HTTP" style="width:70%;" >}}

#### Configuration

Pour définir votre requête HTTP :

1. Choisissez la méthode et l'URL à interroger à l'aide des options **Method** et **URL**. Les méthodes disponibles sont `GET`, `POST`, `PATCH`, `PUT`, `HEAD`, `DELETE` et `OPTIONS`.
2. Si vous le souhaitez, cliquez sur **Advanced Options** pour définir des options avancées :
     * Follow redirects : activez cette fonction pour que l'endpoint surveillé suive jusqu'à dix redirections.
     * Allow insecure certificates : activez cette fonction pour que votre test HTTP poursuive son processus de connexion même lorsqu'une erreur de validation du certificat survient.
     * Headers : les en-têtes définis remplacent les en-têtes par défaut du navigateur.
     * Authentication : authentification HTTP basique avec nom d'utilisateur et mot de passe.
     * Body : corps de la requête et type de corps (`text/plain`, `application/json`, `text/xml`, `text/html`, `application/x-www-form-urlencoded` ou `None`). **Remarque** : la taille du corps de la requête est limitée à 50 Ko.
     * Cookies : les cookies définis sont ajoutés aux cookies de navigateur par défaut. Définissez plusieurs cookies en suivant le format `<COOKIE_NOM1>=<COOKIE_VALEUR1>; <COOKIE_NOM2>=<COOKIE_VALEUR2>`.
3. Cliquez sur **Test URL** pour tester la configuration de votre requête. Un aperçu des données de la réponse s'affiche alors.

{{< img src="synthetics/browser_tests/http_request2.png" alt="Créer une requête HTTP" style="width:80%;" >}}

#### Ajouter des assertions

Si vous le souhaitez, vous pouvez également évaluer la réussite de l'étape en fonction d'assertions exécutées sur la requête HTTP définie :

| Type          | Opérateur                                                                                               | Type de valeur                                                      |
|---------------|--------------------------------------------------------------------------------------------------------|----------------------------------------------------------------|
| body          | `contains`, `does not contain`, `is`, `is not`, <br> `matches`, `does not match`, <br> [`jsonpath`][13], [`xpath`][14] | _Chaîne_ <br> _[Regex][15]_ <br> _Chaîne_, _[Regex][15]_ |
| header        | `contains`, `does not contain`, `is`, `is not`, <br> `matches`, `does not match`                       | _Chaîne_ <br> _[Regex][15]_                                      |
| temps de réponse | `is less than`                                                                                         | _Nombre entier (ms)_                                                  |
| status code   | `is`, `is not`                                                                                         | _Nombre entier_                                                      |

Si vous cliquez sur **Test URL**, les assertions de base sont automatiquement renseignées :

- `Response time` _is less than_ 2000 ms
- `Header content-type` _is_ "valeur renvoyée"
- `Status code` _is_ "valeur renvoyée"

{{< img src="synthetics/browser_tests/assertions.png" alt="Assertions" style="width:80%;" >}}

#### Extraire une variable depuis la réponse

Vous pouvez également extraire une variable à partir de la réponse de votre requête HTTP en parsant les en-têtes ou le corps de la réponse. La valeur de la variable est mise à jour à chaque fois que l'étape de requête HTTP est exécutée.

Pour parser votre variable :

1. Donnez un nom à votre variable en renseignant le champ **Variable Name**. Ce nom doit comporter au moins trois caractères et peut uniquement contenir des lettres majuscules, des chiffres et des underscores.
2. Indiquez si la variable doit être extraite à partir des en-têtes ou du corps de la réponse :

    * Extraire la valeur à partir du **Response Header** : utilisez l'en-tête de réponse complet de votre requête HTTP comme valeur de variable, ou parsez l'en-tête à l'aide d'une [`regex`][15].
    * Extraire la valeur à partir du **Response Body** : utilisez le corps de réponse complet de votre requête HTTP comme valeur de variable, ou parsez le corps avec une [`regex`][15], une expression [`JSONPath`][13] ou une expression [`XPath`[14].

{{< img src="synthetics/browser_tests/extracted_variable.png" alt="Variable extraite à partir de la réponse" style="width:80%;">}}

Une fois créée, cette variable peut être utilisée dans les [étapes suivantes](#utilisation-des-variables) de votre test Browser.

## Utilisation des variables

### Pour les étapes ajoutées manuellement

Tous les champs comportant l'indicateur `{{` prennent en charge les variables :

{{< img src="synthetics/browser_tests/autocomplete.png" alt="Indicateur de saisie automatique de variable" style="width:70%;">}}

Pour consulter toutes les variables disponibles, saisissez `{{` dans le champ de saisie et choisissez la variable qui vous intéresse.

### Pour les étapes enregistrées automatiquement

Si vous souhaitez enregistrer une étape utilisant une variable, vous pouvez utiliser l'icône en forme de main dans la case de votre variable :

{{< img src="synthetics/browser_tests/variable_input.mp4" alt="Saisie via une variable" video="true" width="100%" >}}

Lors de l'enregistrement, cette action entraîne l'injection de la valeur réelle de la variable dans le champ de saisie de votre site Web (ce qui vous permet ensuite de passer aux étapes restantes) et crée une étape `Type text` associée contenant `{{ <NOM_DE_VOTRE_VARIABLE> }}`.
Lors de l'exécution du test, `{{ <NOM_DE_VOTRE_VARIABLE> }}` est systématiquement remplacée par la valeur associée de votre variable.

Certaines variables sont calculées uniquement lors de l'exécution (par exemple, une variable issue d'une requête HTTP ou d'une étape JavaScript). Pour enregistrer une étape via l'une de ces variables, enregistrez-en une avec la valeur réelle de la variable, puis remplacez cette valeur réelle par `{{ <NOM_DE_VOTRE_VARIABLE> }}` lors de la définition de votre étape, avant d'enregistrer votre test.

**Remarque :** si différentes valeurs sont attribuées à une variable dans les étapes de votre test Browser (dans des sous-tests, par exemple), la variable utilise systématiquement la première valeur qui lui a été attribuée.

## Pour aller plus loin

{{< partial name="whats-next/whats-next.html" >}}

[1]: /fr/synthetics/browser_tests/advanced_options/
[2]: /fr/synthetics/browser_tests/advanced_options/#timeout
[3]: https://chrome.google.com/webstore/detail/datadog-test-recorder/kkbncfpddhdmkfmalecgnphegacgejoa
[4]: /fr/synthetics/guide/email-validation/#create-an-email-variable
[5]: /fr/synthetics/browser_tests/actions#use-variables-in-javascript-steps
[6]: /fr/synthetics/guide/testing-file-upload-and-download/#testing-a-file-download
[7]: /fr/synthetics/settings/
[8]: /fr/synthetics/guide/browser-tests-totp
[9]: /fr/synthetics/browser_tests/actions#test-that-an-email-was-received
[10]: /fr/synthetics/browser_tests/actions#go-to-an-email-and-click-on-a-link
[11]: /fr/synthetics/guide/reusing-browser-test-journeys
[12]: /fr/synthetics/browser_tests/advanced_options/#subtests
[13]: https://restfulapi.net/json-jsonpath/
[14]: https://www.w3schools.com/xml/xpath_syntax.asp
[15]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions