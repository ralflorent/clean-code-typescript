# clean-code-typescript [![Tweet](https://img.shields.io/twitter/url/http/shields.io.svg?style=social)](https://twitter.com/intent/tweet?text=Clean%20Code%20Typescript&url=https://github.com/labs42io/clean-code-typescript)

Concepts de "Clean Code (ou code propre)" adaptés à TypeScript.
Inspirés par [clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript).
Ce document est une traduction de ce repository rédigé par [labs42io](https://github.com/labs42io/clean-code-typescript).

## Sommaire

  1. [Introduction](#introduction)
  2. [Variables](#variables)
  3. [Fonctions](#fonctions)
  4. [Objets et Structures de Données](#objets-et-structures-de-données)
  5. [Classes](#classes)
  6. [SOLID](#solid)
  7. [Tests](#tests)
  8. [Opérations Concurrentes](#opérations-concurrentes)
  9. [Gestion des erreurs](#gestion-des-erreurs)
  10. [Formatage du Code](#formatage-du-code)
  11. [Commentaires](#commentaires)
  12. [Traductions](#traductions)

## Introduction

![Humorous image of software quality estimation as a count of how many expletives
you shout when reading code](https://www.osnews.com/images/comics/wtfm.jpg)

Principes de l'ingénierie de logiciels, extraits du livre de Robert C. Martin
[*Clean Code*](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882),
adaptés à TypeScript. Ce n'est pas un guide de style. C'est un guide pour créer des logiciels
[lisibles, réutilisables, et faciles à réorganiser](https://github.com/ryanmcdermott/3rs-of-software-architecture)
en utilisant TypeScript.

Il n'est pas nécessaire de suivre strictement tous les principes mentionés ici,
et sans oublier que peu d'entre eux seront universellement acceptés. Ce ne sont
que des lignes de recommendations et rien de plus, mais ce sont celles codifiées
au cours de nombreuses années d'expérience collective par les auteurs de *Clean Code*.

Notre métier en tant qu'ingénieur de logiciels a un peu plus de 50 ans et on a encore
beaucoup à apprendre. Lorsque l'architecture de logiciels est aussi ancienne que
l'architecture elle-même, on aura peut-être alors des règles plus difficiles à suivre.
Pour l'instant, laissez ce guide vous servir de pierre de touche pour évaluer la
qualité du code TypeScript que vous et votre équipe produisez.

Une dernière chose: les connaître ne fera pas immédiatement de vous un meilleur
développeur de logiciels, et travailler avec eux pendant de nombreuses années ne
signifie pas que vous ne commettrez pas d’erreurs. Chaque portion de code commence
comme un premier échantillon, comme de l'argile humide qui prend sa forme à la fin.
Enfin, on chasse ces imperfections quand on les revise ensemble avec notre équipe.
Ne vous tracassez pas à tout faire durant vos premiers éssais. Cherchez plutôt à
améliorer le code mis en place!

**[⬆ retour en haut](#sommaire)**

## Variables

### Utiliser des noms de variables descriptifs

Distinguer les noms de façon que le lecteur sache ce que les différences offrent.

**Mal:**

```ts
function between<T>(a1: T, a2: T, a3: T): boolean {
  return a2 <= a1 && a1 <= a3;
}

```

**Bien:**

```ts
function between<T>(value: T, left: T, right: T): boolean {
  return left <= value && value <= right;
}
```

**[⬆ retour en haut](#sommaire)**

### Utiliser des noms de variables prononçables

Si vous ne pouvez pas le prononcer, vous ne pouvez pas en discuter sans ressembler à un idiot.

**Mal:**

```ts
type DtaRcrd102 = {
  genymdhms: Date;
  modymdhms: Date;
  pszqint: number;
}
```

**Bien:**

```ts
type Customer = {
  generationTimestamp: Date;
  modificationTimestamp: Date;
  recordId: number;
}
```

**[⬆ retour en haut](#sommaire)**

### Utiliser le même vocabulaire pour le même type de variable

**Mal:**

```ts
function getUserInfo(): User;
function getUserDetails(): User;
function getUserData(): User;
```

**Bien:**

```ts
function getUser(): User;
```

**[⬆ retour en haut](#sommaire)**

### Utiliser des noms consultables

Nous lirons plus de code que nous n'en aurons jamais écrit. Il est important que
le code que nous écrivons soit lisible et consultable. En *ne nommant pas* les
variables qui finissent par être significatives pour comprendre notre programme,
nous blessons nos lecteurs. Rendez vos noms consultables. Des outils comme
[TSLint](https://palantir.github.io/tslint/rules/no-magic-numbers/) peuvent aider
à identifier les constantes sans nom.

**Mal:**

```ts
// What the heck is 86400000 for?
setTimeout(restart, 86400000);
```

**Bien:**

```ts
// Declare them as capitalized named constants.
const MILLISECONDS_IN_A_DAY = 24 * 60 * 60 * 1000;

setTimeout(restart, MILLISECONDS_IN_A_DAY);
```

**[⬆ retour en haut](#sommaire)**

### Utiliser des variables explicatives

**Mal:**

```ts
declare const users: Map<string, User>;

for (const keyValue of users) {
  // iterate through users map
}
```

**Bien:**

```ts
declare const users: Map<string, User>;

for (const [id, user] of users) {
  // iterate through users map
}
```

**[⬆ retour en haut](#sommaire)**

### Évitez le mapping mental

Mieux vaut être explicite qu'implicite.
*La clarté est essentielle.*

**Mal:**

```ts
const u = getUser();
const s = getSubscription();
const t = charge(u, s);
```

**Bien:**

```ts
const user = getUser();
const subscription = getSubscription();
const transaction = charge(user, subscription);
```

**[⬆ retour en haut](#sommaire)**

### Ne pas ajouter de contexte inutile

Si votre nom de classe/type/objet vous dit quelque chose, ne répétez pas cela dans
le nom de votre variable.

**Mal:**

```ts
type Car = {
  carMake: string;
  carModel: string;
  carColor: string;
}

function print(car: Car): void {
  console.log(`${car.carMake} ${car.carModel} (${car.carColor})`);
}
```

**Bien:**

```ts
type Car = {
  make: string;
  model: string;
  color: string;
}

function print(car: Car): void {
  console.log(`${car.make} ${car.model} (${car.color})`);
}
```

**[⬆ retour en haut](#sommaire)**

### Utiliser des arguments par défaut au lieu d'un raccourci ou de conditions

Les arguments par défaut sont souvent plus propres que les raccourcis.

**Mal:**

```ts
function loadPages(count?: number) {
  const loadCount = count !== undefined ? count : 10;
  // ...
}
```

**Bien:**

```ts
function loadPages(count: number = 10) {
  // ...
}
```

**[⬆ retour en haut](#sommaire)**

### Utiliser enum pour documenter l'intention

Les énumérations peuvent vous aider à documenter l'intention du code. Par exemple,
lorsque on veut que les valeurs soient différentes plutôt que la valeur exacte de
celles-ci.

**Mal:**

```ts
const GENRE = {
  ROMANTIC: 'romantic',
  DRAMA: 'drama',
  COMEDY: 'comedy',
  DOCUMENTARY: 'documentary',
}

projector.configureFilm(GENRE.COMEDY);

class Projector {
  // declaration of Projector
  configureFilm(genre) {
    switch (genre) {
      case GENRE.ROMANTIC:
        // some logic to be executed
    }
  }
}
```

**Bien:**

```ts
enum GENRE {
  ROMANTIC,
  DRAMA,
  COMEDY,
  DOCUMENTARY,
}

projector.configureFilm(GENRE.COMEDY);

class Projector {
  // declaration of Projector
  configureFilm(genre) {
    switch (genre) {
      case GENRE.ROMANTIC:
        // some logic to be executed
    }
  }
}
```

**[⬆ retour en haut](#sommaire)**

## Fonctions

### Paramètres d'une fonction (2 or moins idéalement)

Limiter le nombre de paramètres d'une fonction est extrêmement important car cela
facilite le test de votre fonction. En avoir plus de trois conduit à une explosion
combinatoire où chaque argument doit être testé séparément dans des tonnes de cas
différents.

Avoir un ou deux paramètres par fonction est idéal, et trois devraient être évités
si possible. Rien de plus que cela devrait être consolidé. Assez souvent, si vous
avez plus de deux arguments, votre fonction essaie d'en faire trop à la fois.
Dans les cas où ce n'est pas le cas, la plupart du temps, un objet de niveau
supérieur suffit comme argument.

Pensez à utiliser des objets littéraux si vous avez besoin de beaucoup plus de paramètres.

Pour rendre évidentes les attributs que la fonction attend, vous pouvez utiliser
la syntaxe de [déstructuration](https://basarat.gitbook.io/typescript/future-javascript/destructuring).
Cela présente quelques avantages :

1. Quand quelqu'un regarde la signature d’une fonction, il est immédiatement clair
lesquels des attributs sont en train d’être utilisées.

2. Il peut être utilisé pour simuler des paramètres avec des noms.

3. La déstructuration clone également les valeurs primitives spécifiées de l'objet
passé comme paramètre dans la fonction. Cela peut aider à prévenir les effets secondaires.
Remarque: les objets et les tableaux qui sont déstructurés à partir de l'objet
argument ne sont PAS clonés.

4. TypeScript vous avertit des attributs non-utilisés, qui seraient impossibles
sans déstructuration.

**Mal:**

```ts
function createMenu(title: string, body: string, buttonText: string, cancellable: boolean) {
  // ...
}

createMenu('Foo', 'Bar', 'Baz', true);
```

**Bien:**

```ts
function createMenu(options: { title: string, body: string, buttonText: string, cancellable: boolean }) {
  // ...
}

createMenu({
  title: 'Foo',
  body: 'Bar',
  buttonText: 'Baz',
  cancellable: true
});
```

Vous pouvez encore améliorer la lisibilité en utilisant [type aliases (ou type d'alias)](https://www.typescriptlang.org/docs/handbook/advanced-types.html#type-aliases):

```ts

type MenuOptions = { title: string, body: string, buttonText: string, cancellable: boolean };

function createMenu(options: MenuOptions) {
  // ...
}

createMenu({
  title: 'Foo',
  body: 'Bar',
  buttonText: 'Baz',
  cancellable: true
});
```

**[⬆ retour en haut](#sommaire)**

### Les fonctions devraient faire une chose

C'est de loin la règle la plus importante en ingénierie de logiciels. Lorsque
les fonctions font plus d'une chose, elles sont plus difficiles à composer, à
tester et à raisonner. Lorsque vous arrivez à isoler une fonction afin d’exécuter
une seule tâche, elle peut être facilement refactorisée et votre code sera beaucoup
plus net. Si vous ne prenez en compte ce qui est dit dans ce guide, vous serez en
avance sur de nombreux développeurs.

**Mal:**

```ts
function emailClients(clients: Client[]) {
  clients.forEach((client) => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**Bien:**

```ts
function emailClients(clients: Client[]) {
  clients.filter(isActiveClient).forEach(email);
}

function isActiveClient(client: Client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```

**[⬆ retour en haut](#sommaire)**

### Les noms de fonction doivent préciser ce qu'ils font

**Mal:**

```ts
function addToDate(date: Date, month: number): Date {
  // ...
}

const date = new Date();

// It's hard to tell from the function name what is added
addToDate(date, 1);
```

**Bien:**

```ts
function addMonthToDate(date: Date, month: number): Date {
  // ...
}

const date = new Date();
addMonthToDate(date, 1);
```

**[⬆ retour en haut](#sommaire)**

### Les fonctions ne doivent avoir qu'un niveau d'abstraction

Quand vous avez plus d'un niveau d'abstraction, votre fonction en fait généralement
trop. La division des fonctions conduit à une réutilisabilité et à des tests plus faciles.

**Mal:**

```ts
function parseCode(code: string) {
  const REGEXES = [ /* ... */ ];
  const statements = code.split(' ');
  const tokens = [];

  REGEXES.forEach((regex) => {
    statements.forEach((statement) => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach((token) => {
    // lex...
  });

  ast.forEach((node) => {
    // parse...
  });
}
```

**Bien:**

```ts
const REGEXES = [ /* ... */ ];

function parseCode(code: string) {
  const tokens = tokenize(code);
  const syntaxTree = parse(tokens);

  syntaxTree.forEach((node) => {
    // parse...
  });
}

function tokenize(code: string): Token[] {
  const statements = code.split(' ');
  const tokens: Token[] = [];

  REGEXES.forEach((regex) => {
    statements.forEach((statement) => {
      tokens.push( /* ... */ );
    });
  });

  return tokens;
}

function parse(tokens: Token[]): SyntaxTree {
  const syntaxTree: SyntaxTree[] = [];
  tokens.forEach((token) => {
    syntaxTree.push( /* ... */ );
  });

  return syntaxTree;
}
```

**[⬆ retour en haut](#sommaire)**

### Eliminer la partie dupliquée du code

Faites de votre mieux pour éviter la duplication de certaines parties du votre code.
La duplication du code est mauvaise car cela signifie qu'il y a plus d'un endroit
pour modifier quelque chose si vous devez changer une logique.

Imaginez que vous dirigiez un restaurant et que vous gardiez une trace de votre
inventaire: toutes vos tomates, oignons, ail, épices, etc. Si vous avez plusieurs
listes sur lesquelles vous gardez cela, alors toutes doivent être mises à jour
lorsque vous servez un plat avec des tomates. Si vous n'avez qu'une seule liste,
il n'y a qu'un seul endroit à mettre à jour!

Souvent, vous avez du code en double parce que vous avez deux ou plusieurs choses
légèrement différentes, qui partagent beaucoup en commun, mais leurs différences
vous obligent à avoir deux ou plusieurs fonctions distinctes qui font à peu près
les mêmes choses. Supprimer le code en double signifie créer une abstraction qui
peut gérer cet ensemble de choses différentes avec une seule fonction/module/classe.

Avoir la correcte abstraction est essentiel, c'est pourquoi vous devez suivre les
principes [SOLID](#solid). Les mauvaises abstractions peuvent être pires que le
code en double, alors faites attention! Cela dit, si vous pouvez faire une bonne
abstraction, faites-le! Ne vous répétez pas, sinon vous vous retrouverez à actualiser
plusieurs endroits chaque fois que vous voulez changer une chose.

**Mal:**

```ts
function showDeveloperList(developers: Developer[]) {
  developers.forEach((developer) => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();

    const data = {
      expectedSalary,
      experience,
      githubLink
    };

    render(data);
  });
}

function showManagerList(managers: Manager[]) {
  managers.forEach((manager) => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();

    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```

**Bien:**

```ts
class Developer {
  // ...
  getExtraDetails() {
    return {
      githubLink: this.githubLink,
    }
  }
}

class Manager {
  // ...
  getExtraDetails() {
    return {
      portfolio: this.portfolio,
    }
  }
}

function showEmployeeList(employee: Developer | Manager) {
  employee.forEach((employee) => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();
    const extra = employee.getExtraDetails();

    const data = {
      expectedSalary,
      experience,
      extra,
    };

    render(data);
  });
}
```

Vous devez être critique sur la duplication de code. Parfois, il y a un compromis
entre le code dupliqué et une complexité accrue en introduisant une abstraction
inutile. Lorsque deux implémentations de deux modules différents se ressemblent
mais vivent dans des domaines différents, la duplication peut être acceptable et
préférable à l'extraction du code commun. Le code commun extrait dans ce cas
introduit une dépendance indirecte entre les deux modules.

**[⬆ retour en haut](#sommaire)**

### Définir les objets par défaut avec “Object.assign” ou déstructuration

**Mal:**

```ts
type MenuConfig = { title?: string, body?: string, buttonText?: string, cancellable?: boolean };

function createMenu(config: MenuConfig) {
  config.title = config.title || 'Foo';
  config.body = config.body || 'Bar';
  config.buttonText = config.buttonText || 'Baz';
  config.cancellable = config.cancellable !== undefined ? config.cancellable : true;

  // ...
}

createMenu({ body: 'Bar' });
```

**Bien:**

```ts
type MenuConfig = { title?: string, body?: string, buttonText?: string, cancellable?: boolean };

function createMenu(config: MenuConfig) {
  const menuConfig = Object.assign({
    title: 'Foo',
    body: 'Bar',
    buttonText: 'Baz',
    cancellable: true
  }, config);

  // ...
}

createMenu({ body: 'Bar' });
```

Également, vous pouvez utiliser la déstructuration avec des valeurs par défaut:

```ts
type MenuConfig = { title?: string, body?: string, buttonText?: string, cancellable?: boolean };

function createMenu({ title = 'Foo', body = 'Bar', buttonText = 'Baz', cancellable = true }: MenuConfig) {
  // ...
}

createMenu({ body: 'Bar' });
```

Pour éviter tout effet secondaire et tout comportement inattendu en transmettant
explicitement la valeur `undefined` ou `null`, vous pouvez dire au compilateur de
TypeScript de ne pas l'autoriser.
Consultez [`--strictNullChecks`](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-0.html#--strictnullchecks) l'option dans TypeScript.

**[⬆ retour en haut](#sommaire)**

### Ne pas utiliser pas des indicateurs comme paramètres de fonction

Les indicateurs indiquent à votre utilisateur que cette fonction fait plus d'une chose.
Les fonctions devraient faire une chose. Divisez vos fonctions si elles suivent des
chemins de code différents basés sur un booléen.

**Mal:**

```ts
function createFile(name: string, temp: boolean) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Bien:**

```ts
function createTempFile(name: string) {
  createFile(`./temp/${name}`);
}

function createFile(name: string) {
  fs.create(name);
}
```

**[⬆ retour en haut](#sommaire)**

### Éviter les effets secondaires (partie 1)

Une fonction produit un effet secondaire si elle fait autre chose que de prendre
une valeur et de renvoyer une ou plusieurs autres valeurs. Un effet secondaire
pourrait être d'écrire dans un fichier, de modifier une variable globale ou de
transférer accidentellement tout votre argent à un étranger.

Maintenant, vous devez avoir des effets secondaires dans un programme à l'occasion.
Comme dans l'exemple précédent, vous devrez peut-être écrire dans un fichier. Ce
que vous voulez faire, c'est de centraliser où vous faites cela. Ne pas avoir
plusieurs fonctions et classes qui écrivent dans un fichier particulier. Avoir
un service qui le fait. Seul et l'unique.

Le point principal est d'éviter les pièges courants comme le partage d'état entre
des objets sans aucune structure, l'utilisation de types de données mutables qui
peuvent être écrits par n'importe quoi, et ne pas centraliser où se produisent
vos effets secondaires. Si vous pouvez le faire, vous serez plus heureux que la
grande majorité des autres programmeurs.

**Mal:**

```ts
// Global variable referenced by following function.
let name = 'Robert C. Martin';

function toBase64() {
  name = btoa(name);
}

toBase64();
// If we had another function that used this name, now it'd be a Base64 value

console.log(name); // expected to print 'Robert C. Martin' but instead 'Um9iZXJ0IEMuIE1hcnRpbg=='
```

**Bien:**

```ts
const name = 'Robert C. Martin';

function toBase64(text: string): string {
  return btoa(text);
}

const encodedName = toBase64(name);
console.log(name);
```

**[⬆ retour en haut](#sommaire)**

### Éviter les effets secondaires (partie 2)

En JavaScript, les primitives sont passées par valeur et les objets et tableaux
sont passés par référence. Dans le cas d'objets et de tableaux, si votre fonction
modifie un tableau de panier d'achat, par exemple, en ajoutant un article à acheter,
alors toute autre fonction qui utilise ce tableau `cart` sera affectée par cet
ajout. C'est peut-être bien, mais ça peut aussi être mauvais. Imaginons une mauvaise situation:

L'utilisateur clique sur le bouton “Achat”, qui appelle une fonction `purchase`
qui génère une demande réseau et envoie le tableau `cart` au serveur. En raison
d'une mauvaise connexion réseau, la fonction d'achat doit continuer à réessayer
la demande. Maintenant, que se passe-t-il si, dans l'intervalle, l'utilisateur
clique accidentellement sur le bouton `addItemToCart` sur un article qu'il ne
souhaite pas avant le début de la demande réseau? Si cela se produit et que la
demande de réseau commence, alors cette fonction d'achat enverra l'article ajouté
accidentellement car il a une référence à un tableau de panier d'achat que la
fonction `addItemToCart` a modifié en ajoutant un article indésirable.

Une excellente solution serait que `addItemToCart` clone toujours le`cart`, le
modifie et renvoie le clone. Cela garantit qu'aucune autre fonction conservant
une référence du panier ne sera affectée par des modifications.

Deux avertissements à mentionner à cette approche:

1. Il peut y avoir des cas où vous souhaitez réellement modifier l'objet passée
comme paramètre, mais lorsque vous adoptez cette pratique de programmation, vous
constaterez que ces cas sont assez rares. La plupart des choses peuvent être
refactorisées pour n'avoir aucun effet secondaire!
(voir [fonction pure](https://en.wikipedia.org/wiki/Pure_function))

2. Le clonage de gros objets peut être très coûteux en termes de performances.
Heureusement, ce n'est pas un gros problème dans la pratique, car il existe d'excellentes
bibliothèques qui permettent à ce type d'approche de programmation d'être rapide
et moins gourmande en mémoire que pour le clonage manuel d'objets et de tableaux.

**Mal:**

```ts
function addItemToCart(cart: CartItem[], item: Item): void {
  cart.push({ item, date: Date.now() });
};
```

**Bien:**

```ts
function addItemToCart(cart: CartItem[], item: Item): CartItem[] {
  return [...cart, { item, date: Date.now() }];
};
```

**[⬆ retour en haut](#sommaire)**

### Ne pas écrire dans les fonctions globales

Polluer les fonctions globales est une mauvaise pratique en JavaScript car vous
pourriez entrer en conflit avec une autre bibliothèque et l'utilisateur de votre
API ne serait pas plus sage jusqu'à ce qu'il obtienne une exception en production.
Réfléchissons à un exemple: et si vous vouliez étendre la méthode native Array
de JavaScript pour avoir une méthode `diff` qui pourrait montrer la différence
entre deux tableaux? Vous pouvez écrire votre nouvelle fonction dans le
`Array.prototype`, mais elle pourrait entrer en conflit avec une autre bibliothèque
qui a essayé de faire la même chose. Et si cette autre bibliothèque utilisait
simplement `diff` pour trouver la différence entre le premier et le dernier élément
d'un tableau? C'est pourquoi il serait beaucoup mieux d'utiliser simplement des
classes et d'étendre simplement le global `Array`.

**Mal:**

```ts
declare global {
  interface Array<T> {
    diff(other: T[]): Array<T>;
  }
}

if (!Array.prototype.diff) {
  Array.prototype.diff = function <T>(other: T[]): T[] {
    const hash = new Set(other);
    return this.filter(elem => !hash.has(elem));
  };
}
```

**Bien:**

```ts
class MyArray<T> extends Array<T> {
  diff(other: T[]): T[] {
    const hash = new Set(other);
    return this.filter(elem => !hash.has(elem));
  };
}
```

**[⬆ retour en haut](#sommaire)**

### Privilégier la programmation fonctionnelle à la programmation impérative

Privilégiez ce style de programmation quand vous le pouvez.

**Mal:**

```ts
const contributions = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < contributions.length; i++) {
  totalOutput += contributions[i].linesOfCode;
}
```

**Bien:**

```ts
const contributions = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

const totalOutput = contributions
  .reduce((totalLines, output) => totalLines + output.linesOfCode, 0);
```

**[⬆ retour en haut](#sommaire)**

### Encapsuler les conditions

**Mal:**

```ts
if (subscription.isTrial || account.balance > 0) {
  // ...
}
```

**Bien:**

```ts
function canActivateService(subscription: Subscription, account: Account) {
  return subscription.isTrial || account.balance > 0;
}

if (canActivateService(subscription, account)) {
  // ...
}
```

**[⬆ retour en haut](#sommaire)**

### Éviter les conditions négatives

**Mal:**

```ts
function isEmailNotUsed(email: string): boolean {
  // ...
}

if (isEmailNotUsed(email)) {
  // ...
}
```

**Bien:**

```ts
function isEmailUsed(email: string): boolean {
  // ...
}

if (!isEmailUsed(node)) {
  // ...
}
```

**[⬆ retour en haut](#sommaire)**

### Éviter les conditions

Cela semble être une tâche impossible. En entendant cela pour la première fois,
la plupart des gens disent: “Comment suis-je censé faire quoi que ce soit sans
une déclaration `if`?" La réponse est que vous pouvez utiliser le polymorphisme
pour réaliser la même tâche dans de nombreux cas. La deuxième question est
généralement, "c'est bien, mais pourquoi voudrais-je faire ça?" La réponse est
un concept de code propre précédent que nous avons appris: une fonction ne devrait
faire qu'une seule chose. Lorsque vous avez des classes et des fonctions qui ont
des instructions `if`, vous dites à votre utilisateur que votre fonction fait
plus d'une chose. N'oubliez pas, faites juste une chose.

**Mal:**

```ts
class Airplane {
  private type: string;
  // ...

  getCruisingAltitude() {
    switch (this.type) {
      case '777':
        return this.getMaxAltitude() - this.getPassengerCount();
      case 'Air Force One':
        return this.getMaxAltitude();
      case 'Cessna':
        return this.getMaxAltitude() - this.getFuelExpenditure();
      default:
        throw new Error('Unknown airplane type.');
    }
  }

  private getMaxAltitude(): number {
    // ...
  }
}
```

**Bien:**

```ts
abstract class Airplane {
  protected getMaxAltitude(): number {
    // shared logic with subclasses ...
  }

  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```

**[⬆ retour en haut](#sommaire)**

### Éviter la vérification de type de donnée

TypeScript est un superset syntaxique strict de JavaScript et ajoute une vérification
de type statique facultative au langage.
Préférez toujours spécifier des types de variables, des paramètres et des valeurs
de retour pour exploiter toute la puissance des fonctionnalités de TypeScript.
Cela facilite la refactorisation.

**Mal:**

```ts
function travelToTexas(vehicle: Bicycle | Car) {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(currentLocation, new Location('texas'));
  } else if (vehicle instanceof Car) {
    vehicle.drive(currentLocation, new Location('texas'));
  }
}
```

**Bien:**

```ts
type Vehicle = Bicycle | Car;

function travelToTexas(vehicle: Vehicle) {
  vehicle.move(currentLocation, new Location('texas'));
}
```

**[⬆ retour en haut](#sommaire)**

### Ne pas trop optimiser

Les navigateurs modernes font beaucoup d'optimisation sous le capot lors de l'exécution.
Souvent, si vous optimisez, vous perdez simplement votre temps. Il existe de bonnes
[ressources](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)
pour voir où l'optimisation fait défaut. Ciblez ceux en attendant, jusqu'à ce
qu'ils soient corrigés s'ils le peuvent.

**Mal:**

```ts
// On old browsers, each iteration with uncached `list.length` would be costly
// because of `list.length` recomputation. In modern browsers, this is optimized.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Bien:**

```ts
for (let i = 0; i < list.length; i++) {
  // ...
}
```

**[⬆ retour en haut](#sommaire)**

### Éliminer tout code qui ne s’utilise pas

Le code qui ne s’utilise pas est tout aussi mauvais que le code en double. Il n'y
a aucune raison de le conserver dans votre base de code. S'il n'est pas appelé,
débarrassez-vous-en! Il sera toujours sauvegardé en sécurité dans votre historique
de version si vous en avez toujours besoin.

**Mal:**

```ts
function oldRequestModule(url: string) {
  // ...
}

function requestModule(url: string) {
  // ...
}

const req = requestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');
```

**Bien:**

```ts
function requestModule(url: string) {
  // ...
}

const req = requestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');
```

**[⬆ retour en haut](#sommaire)**

### Utiliser des itérateurs et des générateurs

Utilisez des générateurs et des itérables lorsque vous travaillez avec des
collections de données utilisées comme un flux. Il y a quelques bonnes raisons:

- dissocie l'appelé de la mise en œuvre du générateur dans le sens où l'appelé
décide du nombre éléments à accéder
- exécution paresseuse, les éléments sont diffusés à la demande
- prise en charge intégrée pour l'itération d'éléments à l'aide de la syntaxe `for-of`
- les itérables permettent d'implémenter des modèles d'itérateurs optimisés

**Mal:**

```ts
function fibonacci(n: number): number[] {
  if (n === 1) return [0];
  if (n === 2) return [0, 1];

  const items: number[] = [0, 1];
  while (items.length < n) {
    items.push(items[items.length - 2] + items[items.length - 1]);
  }

  return items;
}

function print(n: number) {
  fibonacci(n).forEach(fib => console.log(fib));
}

// Print first 10 Fibonacci numbers.
print(10);
```

**Bien:**

```ts
// Generates an infinite stream of Fibonacci numbers.
// The generator doesn't keep the array of all numbers.
function* fibonacci(): IterableIterator<number> {
  let [a, b] = [0, 1];

  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

function print(n: number) {
  let i = 0;
  for (const fib of fibonacci()) {
    if (i++ === n) break;
    console.log(fib);
  }
}

// Print first 10 Fibonacci numbers.
print(10);
```

Il existe des bibliothèques qui permettent de travailler avec les itérables de
la même manière qu'avec les tableaux natifs, en des méthodes de chaînage comme
`map`, `slice`, `forEach` etc. Voir [itiriri](https://www.npmjs.com/package/itiriri)
pour un exemple de manipulation avancée avec les itérables
(ou [itiriri-async](https://www.npmjs.com/package/itiriri-async) pour la manipulation
des itérables asynchrones).

```ts
import itiriri from 'itiriri';

function* fibonacci(): IterableIterator<number> {
  let [a, b] = [0, 1];

  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

itiriri(fibonacci())
  .take(10)
  .forEach(fib => console.log(fib));
```

**[⬆ retour en haut](#sommaire)**

## Objets et Structures de Données

### Utiliser des "getters" et "setters"

TypeScript supporte la syntaxe getter/setter.

L'utilisation de getters et de setters pour accéder aux données d'objets qui
englobent le comportement pourrait être meilleure que la simple recherche d'un
attribut sur un objet. "Pourquoi?" vous vous demander. Eh bien, voici une liste de raisons:

- Lorsque vous voulez faire plus que d'obtenir l’attribut d'un objet, vous n'avez
pas besoin de rechercher et de modifier chaque accesseur dans votre base de code.
- Valide de façon plus simple en utilisant le mot-clé `set`.
- Encapsule la représentation interne.
- Facile à ajouter un log des activités et la gestion des erreurs.
- Vous pouvez paresseusement charger les attributs de votre objet, disons l'obtenir
à partir d'un serveur.

**Mal:**

```ts
type BankAccount = {
  balance: number;
  // ...
}

const value = 100;
const account: BankAccount = {
  balance: 0,
  // ...
};

if (value < 0) {
  throw new Error('Cannot set negative balance.');
}

account.balance = value;
```

**Bien:**

```ts
class BankAccount {
  private accountBalance: number = 0;

  get balance(): number {
    return this.accountBalance;
  }

  set balance(value: number) {
    if (value < 0) {
      throw new Error('Cannot set negative balance.');
    }

    this.accountBalance = value;
  }

  // ...
}

// Now `BankAccount` encapsulates the validation logic.
// If one day the specifications change, and we need extra validation rule,
// we would have to alter only the `setter` implementation,
// leaving all dependent code unchanged.
const account = new BankAccount();
account.balance = 100;
```

**[⬆ retour en haut](#sommaire)**

### Faire en sorte que les objets aient des membres privés/protégés

TypeScript prend en charge les accesseurs `public` *(par défaut)*, `protected`
et `private` sur les membres de la classe.

**Mal:**

```ts
class Circle {
  radius: number;

  constructor(radius: number) {
    this.radius = radius;
  }

  perimeter() {
    return 2 * Math.PI * this.radius;
  }

  surface() {
    return Math.PI * this.radius * this.radius;
  }
}
```

**Bien:**

```ts
class Circle {
  constructor(private readonly radius: number) {
  }

  perimeter() {
    return 2 * Math.PI * this.radius;
  }

  surface() {
    return Math.PI * this.radius * this.radius;
  }
}
```

**[⬆ retour en haut](#sommaire)**

### Préférer l'immuabilité

Le système de types de TypeScript vous permet de marquer des attributs individuels
sur une interface/classe comme *readonly*. Cela vous permet de travailler de manière
fonctionnelle (une mutation inattendue est mauvaise). Pour les scénarios plus avancés,
il existe un type intégré `Readonly` qui prend un type`T` et marque toutes ses
attributs comme étant “readonly” à l'aide de types mappés
(voir [mapped types ou "types mappés"](https://www.typescriptlang.org/docs/handbook/advanced-types.html#mapped-types)).

**Mal:**

```ts
interface Config {
  host: string;
  port: string;
  db: string;
}
```

**Bien:**

```ts
interface Config {
  readonly host: string;
  readonly port: string;
  readonly db: string;
}
```

Dans le cas des tableaux, vous pouvez créer un tableau de *readonly* en utilisant
`ReadonlyArray<T>`.
N'autorisez pas les modifications telles que `push()` et `fill()`, mais plutôt
utilisez des fonctionnalités telles que `concat()` et `slice()` vu que celles-ci
ne modifient pas la valeur.

**Mal:**

```ts
const array: number[] = [ 1, 3, 5 ];
array = []; // error
array.push(100); // array will updated
```

**Bien:**

```ts
const array: ReadonlyArray<number> = [ 1, 3, 5 ];
array = []; // error
array.push(100); // error
```

Déclarer des paramètres de type "read-only" dans [TypeScript 3.4 est un peu plus facile](https://github.com/microsoft/TypeScript/wiki/What's-new-in-TypeScript#improvements-for-readonlyarray-and-readonly-tuples).

```ts
function hoge(args: readonly string[]) {
  args.push(1); // error
}
```

Préférer les [assertions "const"](https://github.com/microsoft/TypeScript/wiki/What's-new-in-TypeScript#const-assertions)
pour des valeurs littérales.

**Mal:**

```ts
const config = {
  hello: 'world'
};
config.hello = 'world'; // value is changed

const array  = [ 1, 3, 5 ];
array[0] = 10; // value is changed

// writable objects is returned
function readonlyData(value: number) {
  return { value };
}

const result = readonlyData(100);
result.value = 200; // value is changed
```

**Bien:**

```ts
// read-only object
const config = {
  hello: 'world'
} as const;
config.hello = 'world'; // error

// read-only array
const array  = [ 1, 3, 5 ] as const;
array[0] = 10; // error

// You can return read-only objects
function readonlyData(value: number) {
  return { value } as const;
}

const result = readonlyData(100);
result.value = 200; // error
```

**[⬆ retour en haut](#sommaire)**

### type vs interface

Utilisez type lorsque vous pourriez avoir besoin d'une union ou d'une intersection.
Utilisez l'interface lorsque vous voulez “étendre” ou “implémenter”. Il n'y a
cependant pas de règle stricte, utilisez celle qui vous convient.  Pour une
explication plus détaillée, reportez-vous à cette
[réponse](https://stackoverflow.com/questions/37233735/typescript-interfaces-vs-types/54101543#54101543)
sur les différences entre `type` et `interface` dans TypeScript.

**Mal:**

```ts
interface EmailConfig {
  // ...
}

interface DbConfig {
  // ...
}

interface Config {
  // ...
}

//...

type Shape = {
  // ...
}
```

**Bien:**

```ts

type EmailConfig = {
  // ...
}

type DbConfig = {
  // ...
}

type Config  = EmailConfig | DbConfig;

// ...

interface Shape {
  // ...
}

class Circle implements Shape {
  // ...
}

class Square implements Shape {
  // ...
}
```

**[⬆ retour en haut](#sommaire)**

## Classes

### Les classes doivent être petites

La taille de la classe est mesurée par sa responsabilité. Suivant le
*Principe de Responsabilité Unique*, une classe doit être petite.

**Mal:**

```ts
class Dashboard {
  getLanguage(): string { /* ... */ }
  setLanguage(language: string): void { /* ... */ }
  showProgress(): void { /* ... */ }
  hideProgress(): void { /* ... */ }
  isDirty(): boolean { /* ... */ }
  disable(): void { /* ... */ }
  enable(): void { /* ... */ }
  addSubscription(subscription: Subscription): void { /* ... */ }
  removeSubscription(subscription: Subscription): void { /* ... */ }
  addUser(user: User): void { /* ... */ }
  removeUser(user: User): void { /* ... */ }
  goToHomePage(): void { /* ... */ }
  updateProfile(details: UserDetails): void { /* ... */ }
  getVersion(): string { /* ... */ }
  // ...
}

```

**Bien:**

```ts
class Dashboard {
  disable(): void { /* ... */ }
  enable(): void { /* ... */ }
  getVersion(): string { /* ... */ }
}

// split the responsibilities by moving the remaining methods to other classes
// ...
```

**[⬆ retour en haut](#sommaire)**

### Haute cohésion et faible couplage

La cohésion définit le degré de relation entre les membres de la classe. Idéalement,
tous les champs d'une classe doivent être utilisés par chaque méthode. On dit alors
que la classe est au maximum cohérente. En pratique, cela n'est cependant pas toujours
possible, ni même conseillé. Vous devez cependant préférer une cohésion élevée.

Le couplage fait référence à la façon dont les parents sont liés ou dépendants
entre eux. Les classes sont dites à faible couplage si les changements dans l'un
d'entre eux n'affectent pas l'autre.

Une bonne conception logicielle a une **haute cohésion** et un **faible couplage**.

**Mal:**

```ts
class UserManager {
  // Bad: each private variable is used by one or another group of methods.
  // It makes clear evidence that the class is holding more than a single responsibility.
  // If I need only to create the service to get the transactions for a user,
  // I'm still forced to pass and instance of `emailSender`.
  constructor(
    private readonly db: Database,
    private readonly emailSender: EmailSender) {
  }

  async getUser(id: number): Promise<User> {
    return await db.users.findOne({ id });
  }

  async getTransactions(userId: number): Promise<Transaction[]> {
    return await db.transactions.find({ userId });
  }

  async sendGreeting(): Promise<void> {
    await emailSender.send('Welcome!');
  }

  async sendNotification(text: string): Promise<void> {
    await emailSender.send(text);
  }

  async sendNewsletter(): Promise<void> {
    // ...
  }
}
```

**Bien:**

```ts
class UserService {
  constructor(private readonly db: Database) {
  }

  async getUser(id: number): Promise<User> {
    return await this.db.users.findOne({ id });
  }

  async getTransactions(userId: number): Promise<Transaction[]> {
    return await this.db.transactions.find({ userId });
  }
}

class UserNotifier {
  constructor(private readonly emailSender: EmailSender) {
  }

  async sendGreeting(): Promise<void> {
    await this.emailSender.send('Welcome!');
  }

  async sendNotification(text: string): Promise<void> {
    await this.emailSender.send(text);
  }

  async sendNewsletter(): Promise<void> {
    // ...
  }
}
```

**[⬆ retour en haut](#sommaire)**

### Préférer la composition à l'héritage

Comme indiqué dans [Design Patterns](https://en.wikipedia.org/wiki/Design_Patterns)
du “Gang of Four”, *vous devriez préférer la composition à l'héritage* où vous le
pouvez. Il y a beaucoup de bonnes raisons d'utiliser l'héritage et beaucoup de
bonnes raisons d'utiliser la composition. Le point principal de cette maxime est
que si votre esprit va instinctivement à l'héritage, essayez de penser si la
composition pourrait mieux modéliser votre problème. Dans certains cas, c'est possible.

Vous vous demandez peut-être alors "quand dois-je utiliser l'héritage?" Cela
dépend de votre problème, mais c'est une liste décente où l'héritage a plus de
sens que la composition:

1. Votre héritage représente une relation "est-une" et non une relation "a-une"
(Humain-> Animal vs Utilisateur-> Détails de l'utilisateur).

2. Vous pouvez réutiliser le code des classes de base (les humains peuvent se
déplacer comme tous les animaux).

3. Vous souhaitez apporter des modifications globales aux classes dérivées en
modifiant une classe de base. (Modifiez la dépense calorique de tous les animaux
lorsqu'ils se déplacent).

**Mal:**

```ts
class Employee {
  constructor(
    private readonly name: string,
    private readonly email: string) {
  }

  // ...
}

// Bad because Employees "have" tax data. EmployeeTaxData is not a type of Employee
class EmployeeTaxData extends Employee {
  constructor(
    name: string,
    email: string,
    private readonly ssn: string,
    private readonly salary: number) {
    super(name, email);
  }

  // ...
}
```

**Bien:**

```ts
class Employee {
  private taxData: EmployeeTaxData;

  constructor(
    private readonly name: string,
    private readonly email: string) {
  }

  setTaxData(ssn: string, salary: number): Employee {
    this.taxData = new EmployeeTaxData(ssn, salary);
    return this;
  }

  // ...
}

class EmployeeTaxData {
  constructor(
    public readonly ssn: string,
    public readonly salary: number) {
  }

  // ...
}
```

**[⬆ retour en haut](#sommaire)**

### Utiliser le chaînage des méthodes

Ce modèle est très utile et couramment utilisé dans de nombreuses bibliothèques.
Il permet à votre code d'être expressif et moins verbeux. Pour cette raison,
utilisez le chaînage de méthodes et regardez à quel point votre code sera propre.

**Mal:**

```ts
class QueryBuilder {
  private collection: string;
  private pageNumber: number = 1;
  private itemsPerPage: number = 100;
  private orderByFields: string[] = [];

  from(collection: string): void {
    this.collection = collection;
  }

  page(number: number, itemsPerPage: number = 100): void {
    this.pageNumber = number;
    this.itemsPerPage = itemsPerPage;
  }

  orderBy(...fields: string[]): void {
    this.orderByFields = fields;
  }

  build(): Query {
    // ...
  }
}

// ...

const queryBuilder = new QueryBuilder();
queryBuilder.from('users');
queryBuilder.page(1, 100);
queryBuilder.orderBy('firstName', 'lastName');

const query = queryBuilder.build();
```

**Bien:**

```ts
class QueryBuilder {
  private collection: string;
  private pageNumber: number = 1;
  private itemsPerPage: number = 100;
  private orderByFields: string[] = [];

  from(collection: string): this {
    this.collection = collection;
    return this;
  }

  page(number: number, itemsPerPage: number = 100): this {
    this.pageNumber = number;
    this.itemsPerPage = itemsPerPage;
    return this;
  }

  orderBy(...fields: string[]): this {
    this.orderByFields = fields;
    return this;
  }

  build(): Query {
    // ...
  }
}

// ...

const query = new QueryBuilder()
  .from('users')
  .page(1, 100)
  .orderBy('firstName', 'lastName')
  .build();
```

**[⬆ retour en haut](#sommaire)**

## SOLID

### Principe de Responsabilité Unique ou "Single Responsibility Principle (SRP)"

Comme indiqué dans Clean Code, "Il ne devrait jamais y avoir plus d'une raison
pour qu'une classe change". Il est tentant d'emballer une classe avec beaucoup
de fonctionnalités, comme lorsque vous ne pouvez emporter qu'une seule valise
pendant votre vol. Le problème avec cela est que votre classe ne sera pas
conceptuellement cohérente et cela lui donnera de nombreuses raisons de changer.
Il est important de minimiser le nombre de fois que vous devez changer de classe.
C'est important car si trop de fonctionnalités sont dans une classe et que vous
en modifiez une partie, il peut être difficile de comprendre comment cela affectera
les autres modules dépendants de votre base de code.

**Mal:**

```ts
class UserSettings {
  constructor(private readonly user: User) {
  }

  changeSettings(settings: UserSettings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**Bien:**

```ts
class UserAuth {
  constructor(private readonly user: User) {
  }

  verifyCredentials() {
    // ...
  }
}


class UserSettings {
  private readonly auth: UserAuth;

  constructor(private readonly user: User) {
    this.auth = new UserAuth(user);
  }

  changeSettings(settings: UserSettings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```

**[⬆ retour en haut](#sommaire)**

### Principe ouvert/fermé ou "Open/Closed Principle (OCP)"

Comme l'a déclaré Bertrand Meyer, "les entités logicielles (classes, modules,
fonctions, etc.) devraient être ouvertes pour extension, mais fermées pour modification."
Mais qu'est-ce que cela signifie? Ce principe stipule essentiellement que vous
devez autoriser les utilisateurs à ajouter de nouvelles fonctionnalités sans modifier le code existant.

**Mal:**

```ts
class AjaxAdapter extends Adapter {
  constructor() {
    super();
  }

  // ...
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
  }

  // ...
}

class HttpRequester {
  constructor(private readonly adapter: Adapter) {
  }

  async fetch<T>(url: string): Promise<T> {
    if (this.adapter instanceof AjaxAdapter) {
      const response = await makeAjaxCall<T>(url);
      // transform response and return
    } else if (this.adapter instanceof NodeAdapter) {
      const response = await makeHttpCall<T>(url);
      // transform response and return
    }
  }
}

function makeAjaxCall<T>(url: string): Promise<T> {
  // request and return promise
}

function makeHttpCall<T>(url: string): Promise<T> {
  // request and return promise
}
```

**Bien:**

```ts
abstract class Adapter {
  abstract async request<T>(url: string): Promise<T>;

  // code shared to subclasses ...
}

class AjaxAdapter extends Adapter {
  constructor() {
    super();
  }

  async request<T>(url: string): Promise<T>{
    // request and return promise
  }

  // ...
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
  }

  async request<T>(url: string): Promise<T>{
    // request and return promise
  }

  // ...
}

class HttpRequester {
  constructor(private readonly adapter: Adapter) {
  }

  async fetch<T>(url: string): Promise<T> {
    const response = await this.adapter.request<T>(url);
    // transform response and return
  }
}
```

**[⬆ retour en haut](#sommaire)**

### Principe de substitution de Liskov ou "Liskov Substitution Principle (LSP)"

C'est un terme effrayant pour un concept très simple. Il est formellement défini
comme "Si S est un sous-type de T, alors les objets de type T peuvent être remplacés
par des objets de type S (c'est-à-dire que les objets de type S peuvent remplacer
des objets de type T) sans altérer aucune des attributs souhaitables de ce
programme (correction, tâche effectuée, etc.)." C'est une définition encore plus effrayante.

La meilleure explication est que si vous avez une classe parent et une classe enfant,
la classe parent et la classe enfant peuvent être utilisées de manière interchangeable
sans obtenir de résultats incorrects. Cela peut encore prêter à confusion, alors
jetons un coup d'œil à l'exemple classique de Square-Rectangle. Mathématiquement,
un carré est un rectangle, mais si vous le modélisez en utilisant la relation "is-a"
via l'héritage, vous rencontrez rapidement des problèmes.

**Mal:**

```ts
class Rectangle {
  constructor(
    protected width: number = 0,
    protected height: number = 0) {

  }

  setColor(color: string): this {
    // ...
  }

  render(area: number) {
    // ...
  }

  setWidth(width: number): this {
    this.width = width;
    return this;
  }

  setHeight(height: number): this {
    this.height = height;
    return this;
  }

  getArea(): number {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width: number): this {
    this.width = width;
    this.height = width;
    return this;
  }

  setHeight(height: number): this {
    this.width = height;
    this.height = height;
    return this;
  }
}

function renderLargeRectangles(rectangles: Rectangle[]) {
  rectangles.forEach((rectangle) => {
    const area = rectangle
      .setWidth(4)
      .setHeight(5)
      .getArea(); // BAD: Returns 25 for Square. Should be 20.
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**Bien:**

```ts
abstract class Shape {
  setColor(color: string): this {
    // ...
  }

  render(area: number) {
    // ...
  }

  abstract getArea(): number;
}

class Rectangle extends Shape {
  constructor(
    private readonly width = 0,
    private readonly height = 0) {
    super();
  }

  getArea(): number {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(private readonly length: number) {
    super();
  }

  getArea(): number {
    return this.length * this.length;
  }
}

function renderLargeShapes(shapes: Shape[]) {
  shapes.forEach((shape) => {
    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)];
renderLargeShapes(shapes);
```

**[⬆ retour en haut](#sommaire)**

### Principe de ségrégation d'interface ou "Interface Segregation Principle (ISP)"

L'ISP déclare que "les clients ne devraient pas être obligés de dépendre d'interfaces
qu'ils n'utilisent pas.". Ce principe est très lié au principe de responsabilité unique.
Ce que cela signifie vraiment, c'est que vous devez toujours concevoir vos abstractions
de manière à ce que les clients qui utilisent les méthodes exposées n'obtiennent
pas tout le gâteau à la place. Cela implique également d'imposer aux clients la
charge de mettre en œuvre des méthodes dont ils n'ont pas réellement besoin.

**Mal:**

```ts
interface SmartPrinter {
  print();
  fax();
  scan();
}

class AllInOnePrinter implements SmartPrinter {
  print() {
    // ...
  }

  fax() {
    // ...
  }

  scan() {
    // ...
  }
}

class EconomicPrinter implements SmartPrinter {
  print() {
    // ...
  }

  fax() {
    throw new Error('Fax not supported.');
  }

  scan() {
    throw new Error('Scan not supported.');
  }
}
```

**Bien:**

```ts
interface Printer {
  print();
}

interface Fax {
  fax();
}

interface Scanner {
  scan();
}

class AllInOnePrinter implements Printer, Fax, Scanner {
  print() {
    // ...
  }

  fax() {
    // ...
  }

  scan() {
    // ...
  }
}

class EconomicPrinter implements Printer {
  print() {
    // ...
  }
}
```

**[⬆ retour en haut](#sommaire)**

### Principe d'inversion de dépendance ou "Dependency Inversion Principle (DIP)"

Ce principe énonce deux choses essentielles:

1. Les modules de haut niveau ne doivent pas dépendre de modules de bas niveau.
Les deux devraient dépendre d'abstractions.
2. Les abstractions ne devraient pas dépendre des détails. Les détails doivent
dépendre des abstractions.

Cela peut être difficile à comprendre au début, mais si vous avez travaillé avec
Angular, vous avez vu une implémentation de ce principe sous la forme d'une
injection de dépendance (DI). Bien qu'il ne s'agisse pas de concepts identiques,
DIP empêche les modules de haut niveau de connaître les détails de ses modules de
bas niveau et de les configurer. Il peut y parvenir grâce à DI. Un énorme avantage
de ceci est qu'il réduit le couplage entre les modules. Le couplage est un très
mauvais schéma de développement car il rend votre code difficile à refactoriser.

Le DIP est généralement obtenu en utilisant un conteneur d'inversion de contrôle
(IoC). Un exemple de conteneur IoC puissant pour TypeScript est
[InversifyJs](https://www.npmjs.com/package/inversify).

**Mal:**

```ts
import { readFile as readFileCb } from 'fs';
import { promisify } from 'util';

const readFile = promisify(readFileCb);

type ReportData = {
  // ..
}

class XmlFormatter {
  parse<T>(content: string): T {
    // Converts an XML string to an object T
  }
}

class ReportReader {

  // BAD: We have created a dependency on a specific request implementation.
  // We should just have ReportReader depend on a parse method: `parse`
  private readonly formatter = new XmlFormatter();

  async read(path: string): Promise<ReportData> {
    const text = await readFile(path, 'UTF8');
    return this.formatter.parse<ReportData>(text);
  }
}

// ...
const reader = new ReportReader();
await report = await reader.read('report.xml');
```

**Bien:**

```ts
import { readFile as readFileCb } from 'fs';
import { promisify } from 'util';

const readFile = promisify(readFileCb);

type ReportData = {
  // ..
}

interface Formatter {
  parse<T>(content: string): T;
}

class XmlFormatter implements Formatter {
  parse<T>(content: string): T {
    // Converts an XML string to an object T
  }
}


class JsonFormatter implements Formatter {
  parse<T>(content: string): T {
    // Converts a JSON string to an object T
  }
}

class ReportReader {
  constructor(private readonly formatter: Formatter) {
  }

  async read(path: string): Promise<ReportData> {
    const text = await readFile(path, 'UTF8');
    return this.formatter.parse<ReportData>(text);
  }
}

// ...
const reader = new ReportReader(new XmlFormatter());
await report = await reader.read('report.xml');

// or if we had to read a json report
const reader = new ReportReader(new JsonFormatter());
await report = await reader.read('report.json');
```

**[⬆ retour en haut](#sommaire)**

## Tests

Les tests sont plus importants que l'expédition. Si vous n'avez aucun test ou une
quantité insuffisante, chaque fois que vous expédiez du code, vous ne serez pas
sûr de ne rien casser.
Décider de ce qui constitue un montant adéquat appartient à votre équipe, mais
avoir une couverture à 100% (tous les relevés et succursales)
est la façon dont vous obtenez une confiance très élevée et une tranquillité
d'esprit de développeur Cela signifie qu'en plus d'avoir un excellent cadre de
test, vous devez également utiliser un bon [outil de couverture](https://github.com/gotwarlost/istanbul).

Il n'y a aucune excuse pour ne pas écrire de tests. Il existe [beaucoup de bons frameworks de test JS](http://jstherightway.org/#testing-tools) avec prise en charge des typages pour TypeScript, alors trouvez
celui que votre équipe préfère. Lorsque vous en trouvez un qui fonctionne pour
votre équipe, essayez de toujours écrire des tests pour chaque nouvelle
fonctionnalité/module que vous introduisez. Si votre méthode préférée est le
développement piloté par les tests (TDD), c'est très bien, mais le principal
est de vous assurer que vous atteignez vos objectifs de couverture avant de
lancer une fonctionnalité ou de refactoriser une fonctionnalité existante.

### Les trois lois du TDD

1. Vous n'êtes pas autorisé à écrire un code de production, sauf pour effectuer
un test unitaire ayant échoué.

2. Vous n'êtes pas autorisé à écrire plus d'un test unitaire que ce qui est
suffisant pour échouer; et les échecs de compilation sont des échecs.

3. Vous n'êtes pas autorisé à écrire plus de code de production qu'il n'en faut
pour réussir le test unitaire ayant échoué.

**[⬆ retour en haut](#sommaire)**

### Les règles de F.I.R.S.T

Les tests propres doivent suivre les règles:

- **Fast**: les tests *rapides* doivent être rapides car nous voulons les exécuter fréquemment.

- **Independent**: les tests *indépendants* ne devraient pas dépendre les uns des autres.
Ils doivent fournir la même sortie, qu'ils soient exécutés indépendamment ou tous
ensemble dans n'importe quel ordre.

- **Repeatable**: les tests doivent être reproductibles dans tous les environnements et
ne soyez pas une excuse pour pourquoi ils échouent.

- **Self-Validating**: un test doit répondre avec *Réussi* ou *Échoué*. Vous n'avez
pas besoin de comparer les fichiers journaux pour répondre si un test a réussi.

- **Timely**: des tests unitaires *opportuns* doivent être écrits avant le code de
production. Si vous écrivez des tests après le code de production, vous pourriez
trouver les tests d'écriture trop difficiles.

**[⬆ retour en haut](#sommaire)**

### Concept unique par test

Les tests doivent également suivre le *principe de responsabilité unique*. Faites
une seule assertion par test unitaire.

**Mal:**

```ts
import { assert } from 'chai';

describe('AwesomeDate', () => {
  it('handles date boundaries', () => {
    let date: AwesomeDate;

    date = new AwesomeDate('1/1/2015');
    assert.equal('1/31/2015', date.addDays(30));

    date = new AwesomeDate('2/1/2016');
    assert.equal('2/29/2016', date.addDays(28));

    date = new AwesomeDate('2/1/2015');
    assert.equal('3/1/2015', date.addDays(28));
  });
});
```

**Bien:**

```ts
import { assert } from 'chai';

describe('AwesomeDate', () => {
  it('handles 30-day months', () => {
    const date = new AwesomeDate('1/1/2015');
    assert.equal('1/31/2015', date.addDays(30));
  });

  it('handles leap year', () => {
    const date = new AwesomeDate('2/1/2016');
    assert.equal('2/29/2016', date.addDays(28));
  });

  it('handles non-leap year', () => {
    const date = new AwesomeDate('2/1/2015');
    assert.equal('3/1/2015', date.addDays(28));
  });
});
```

**[⬆ retour en haut](#sommaire)**

### Le nom du test doit révéler son intention

Lorsqu'un test échoue, son nom est la première indication de ce qui peut avoir mal tourné.

**Mal:**

```ts
describe('Calendar', () => {
  it('2/29/2020', () => {
    // ...
  });

  it('throws', () => {
    // ...
  });
});
```

**Bien:**

```ts
describe('Calendar', () => {
  it('should handle leap year', () => {
    // ...
  });

  it('should throw when format is invalid', () => {
    // ...
  });
});
```

**[⬆ retour en haut](#sommaire)**

## Opérations Concurrentes

### Préférer les promesses aux "callbacks"

Les callbacks ne sont pas propres et provoquent des quantités excessives
d'imbrication *(l'enfer des callbacks)*. Il existe des utilitaires qui transforment
les fonctions existantes en utilisant le style du callback en une version qui renvoie
des promesses (pour Node.js,
voir [`util.promisify`](https://nodejs.org/dist/latest-v8.x/docs/api/util.html#util_util_promisify_original),
pour un usage général, voir [pify](https://www.npmjs.com/package/pify), [es6-promisify](https://www.npmjs.com/package/es6-promisify))

**Mal:**

```ts
import { get } from 'request';
import { writeFile } from 'fs';

function downloadPage(url: string, saveTo: string, callback: (error: Error, content?: string) => void) {
  get(url, (error, response) => {
    if (error) {
      callback(error);
    } else {
      writeFile(saveTo, response.body, (error) => {
        if (error) {
          callback(error);
        } else {
          callback(null, response.body);
        }
      });
    }
  });
}

downloadPage('https://en.wikipedia.org/wiki/Robert_Cecil_Martin', 'article.html', (error, content) => {
  if (error) {
    console.error(error);
  } else {
    console.log(content);
  }
});
```

**Bien:**

```ts
import { get } from 'request';
import { writeFile } from 'fs';
import { promisify } from 'util';

const write = promisify(writeFile);

function downloadPage(url: string, saveTo: string): Promise<string> {
  return get(url)
    .then(response => write(saveTo, response));
}

downloadPage('https://en.wikipedia.org/wiki/Robert_Cecil_Martin', 'article.html')
  .then(content => console.log(content))
  .catch(error => console.error(error));
```

Les promesses prennent en charge quelques méthodes d'assistance qui aident à rendre le code plus concis:

| Modèle                  | Description                                |
| ------------------------ | -----------------------------------------|
| `Promise.resolve(value)` | Convertit une valeur en promesse résolue.|
| `Promise.reject(error)`  | Convertit une erreur en une promesse rejetée.  |
| `Promise.all(promises)`  | Renvoie une nouvelle promesse qui est remplie avec un tableau de valeurs de réalisation pour les promesses ou les refus passés avec la raison de la première promesse qui rejette. |
| `Promise.race(promises)`| Renvoie une nouvelle promesse qui est remplie/rejetée avec le résultat/l'erreur de la première promesse réglée à partir du tableau des promesses passées.|

`Promise.all` est particulièrement utile lorsqu'il est nécessaire d'exécuter
des tâches en parallèle. `Promise.race` facilite l'implémentation de choses comme
les délais d'attente pour les promesses.

**[⬆ retour en haut](#sommaire)**

### Async/Await sont encore plus propres que les promesses

Avec la syntaxe `async`/`await`, vous pouvez écrire du code beaucoup plus propre et
plus compréhensible que les promesses enchaînées. Dans une fonction préfixée par
le mot clé `async`, vous avez un moyen de dire au runtime de JavaScript de suspendre
l'exécution du code sur le mot clé`await` (lorsqu'il est utilisé sur une promesse).

**Mal:**

```ts
import { get } from 'request';
import { writeFile } from 'fs';
import { promisify } from 'util';

const write = util.promisify(writeFile);

function downloadPage(url: string, saveTo: string): Promise<string> {
  return get(url).then(response => write(saveTo, response));
}

downloadPage('https://en.wikipedia.org/wiki/Robert_Cecil_Martin', 'article.html')
  .then(content => console.log(content))
  .catch(error => console.error(error));
```

**Bien:**

```ts
import { get } from 'request';
import { writeFile } from 'fs';
import { promisify } from 'util';

const write = promisify(writeFile);

async function downloadPage(url: string, saveTo: string): Promise<string> {
  const response = await get(url);
  await write(saveTo, response);
  return response;
}

// somewhere in an async function
try {
  const content = await downloadPage('https://en.wikipedia.org/wiki/Robert_Cecil_Martin', 'article.html');
  console.log(content);
} catch (error) {
  console.error(error);
}
```

**[⬆ retour en haut](#sommaire)**

## Gestion des Erreurs

Les erreurs lancées sont une bonne chose! Elles signifient que le runtime a
réussi à identifier quand quelque chose dans votre programme a mal tourné et
qu'il vous informe en arrêtant la fonction exécution sur la pile actuelle, tuant
le processus (dans Node), et vous notifiant dans la console avec une trace de pile.

### Utiliser toujours "Error" pour lancer ou rejeter une erreur

JavaScript, ainsi que TypeScript, vous permet de `throw` ou "lancer" n'importe quel
objet. Une promesse peut également être rejetée avec n'importe quel objet de motif.
Il est conseillé d'utiliser la syntaxe `throw` avec un type`Error`. C'est parce que
votre erreur peut être interceptée dans un code de niveau supérieur avec une syntaxe `catch`.
Il serait très déroutant d’y attraper un message de chaîne et
[débogage plus douloureux](https://basarat.gitbook.io/typescript/type-system/exceptions#always-use-error).
Pour la même raison, vous devez rejeter les promesses avec des types `Error`.

**Mal:**

```ts
function calculateTotal(items: Item[]): number {
  throw 'Not implemented.';
}

function get(): Promise<Item[]> {
  return Promise.reject('Not implemented.');
}
```

**Bien:**

```ts
function calculateTotal(items: Item[]): number {
  throw new Error('Not implemented.');
}

function get(): Promise<Item[]> {
  return Promise.reject(new Error('Not implemented.'));
}

// or equivalent to:

async function get(): Promise<Item[]> {
  throw new Error('Not implemented.');
}
```

L'avantage de l'utilisation des types `Error` est qu'il est supporté par la
syntaxe`try/catch/finally` et implicitement toutes les erreurs ont l'attribut
`stack` qui est très puissante pour le débogage. Il existe également d'autres
alternatives, pour ne pas utiliser la syntaxe `throw` et renvoyer à la place
toujours des objets d'erreur personnalisés. TypeScript rend cela encore plus
facile. Prenons l'exemple suivant:

```ts
type Result<R> = { isError: false, value: R };
type Failure<E> = { isError: true, error: E };
type Failable<R, E> = Result<R> | Failure<E>;

function calculateTotal(items: Item[]): Failable<number, 'empty'> {
  if (items.length === 0) {
    return { isError: true, error: 'empty' };
  }

  // ...
  return { isError: false, value: 42 };
}
```

Pour l'explication détaillée de cette idée, reportez-vous à la
[publication d'origine](https://medium.com/@dhruvrajvanshi/making-exceptions-type-safe-in-typescript-c4d200ee78e9).

**[⬆ retour en haut](#sommaire)**

### Ne pas ignorer pas les erreurs capturées

Ne rien faire avec une erreur détectée ne vous donne pas la possibilité de corriger
ou de réagir à cette erreur. L'enregistrement de l'erreur sur la console (`console.log`)
n'est pas beaucoup mieux car il arrive souvent qu'elle se perde dans un océan de
choses imprimées sur la console. Si vous enveloppez un morceau de code dans un
`try/catch` cela signifie que vous pensez qu'une erreur peut s'y produire et que
vous devez donc avoir un plan, ou créer un chemin de code, pour quand il se produit.

**Mal:**

```ts
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}

// or even worse

try {
  functionThatMightThrow();
} catch (error) {
  // ignore error
}
```

**Bien:**

```ts
import { logger } from './logging'

try {
  functionThatMightThrow();
} catch (error) {
  logger.log(error);
}
```

**[⬆ retour en haut](#sommaire)**

### Ne pas ignorer pas les promesses rejetées

Pour la même raison, vous ne devez pas ignorer les erreurs interceptées de `try/catch`.

**Mal:**

```ts
getUser()
  .then((user: User) => {
    return sendEmail(user.email, 'Welcome!');
  })
  .catch((error) => {
    console.log(error);
  });
```

**Bien:**

```ts
import { logger } from './logging'

getUser()
  .then((user: User) => {
    return sendEmail(user.email, 'Welcome!');
  })
  .catch((error) => {
    logger.log(error);
  });

// or using the async/await syntax:

try {
  const user = await getUser();
  await sendEmail(user.email, 'Welcome!');
} catch (error) {
  logger.log(error);
}
```

**[⬆ retour en haut](#sommaire)**

## Formatage du Code

Le formatage est subjectif. Comme beaucoup de règles ici, il n'y a pas de règle
stricte que vous devez suivre. Le but principal est *NE PAS DISCUTER* sur le formatage.
Il existe des tonnes d'outils pour automatiser cela. Utilisez-en un! C'est une perte
de temps et d'argent pour les ingénieurs de discuter du formatage. La règle générale
à suivre est de *conserver des règles de formatage cohérentes*.

Pour TypeScript, il existe un outil puissant appelé [TSLint](https://palantir.github.io/tslint/).
Il s'agit d'un outil d'analyse statique qui peut vous aider à améliorer considérablement
la lisibilité et la maintenabilité de votre code. Il existe des configurations
TSLint prêtes à l'emploi que vous pouvez référencer dans vos projets:

- [TSLint Config Standard](https://www.npmjs.com/package/tslint-config-standard) - règles de style standard

- [TSLint Config Airbnb](https://www.npmjs.com/package/tslint-config-airbnb) - guide de style Airbnb

- [TSLint Clean Code](https://www.npmjs.com/package/tslint-clean-code) - Les règles TSLint inspirées du [Clean Code: A Handbook of Agile Software Craftsmanship](https://www.amazon.ca/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)

- [TSLint react](https://www.npmjs.com/package/tslint-react) - règles sur les peluches liées à React et JSX

- [TSLint + Prettier](https://www.npmjs.com/package/tslint-config-prettier) - règles de "lint" pour [Prettier](https://github.com/prettier/prettier) formateur de code

- [ESLint rules for TSLint](https://www.npmjs.com/package/tslint-eslint-rules) - règles ESLint pour TypeScript

- [Immutable](https://www.npmjs.com/package/tslint-immutable) - règles pour désactiver la mutation dans TypeScript

Reportez-vous également à cette excellente source
[TypeScript StyleGuide and Coding Conventions](https://basarat.gitbook.io/typescript/styleguide).

### Utiliser une capitalisation cohérente

La capitalisation vous en dit long sur vos variables, fonctions, etc. Ces règles
sont subjectives, donc votre équipe peut choisir ce qu'elle veut. Le fait est,
peu importe ce que vous choisissez tous, juste *soyez cohérent*.

**Mal:**

```ts
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'];
const Artists = ['ACDC', 'Led Zeppelin', 'The Beatles'];

function eraseDatabase() {}
function restore_database() {}

type animal = { /* ... */ }
type Container = { /* ... */ }
```

**Bien:**

```ts
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const SONGS = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'];
const ARTISTS = ['ACDC', 'Led Zeppelin', 'The Beatles'];

function eraseDatabase() {}
function restoreDatabase() {}

type Animal = { /* ... */ }
type Container = { /* ... */ }
```

Utiliser `PascalCase` de préférence pour les noms de classe, d'interface, de type et d'espace de noms.
Utiliser `camelCase` de préférence pour les variables, les fonctions et les membres de la classe.

**[⬆ retour en haut](#sommaire)**

### Les "callers" et "callees" doivent être proches

Si une fonction en appelle une autre, gardez ces fonctions verticalement fermées
dans le fichier source. Idéalement, gardez le "caller" juste au-dessus du "callee".
Nous avons tendance à lire le code de haut en bas, comme un journal. Pour cette raison,
faites lire votre code de cette façon.

**Mal:**

```ts
class PerformanceReview {
  constructor(private readonly employee: Employee) {
  }

  private lookupPeers() {
    return db.lookup(this.employee.id, 'peers');
  }

  private lookupManager() {
    return db.lookup(this.employee, 'manager');
  }

  private getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  review() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();

    // ...
  }

  private getManagerReview() {
    const manager = this.lookupManager();
  }

  private getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.review();
```

**Bien:**

```ts
class PerformanceReview {
  constructor(private readonly employee: Employee) {
  }

  review() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();

    // ...
  }

  private getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  private lookupPeers() {
    return db.lookup(this.employee.id, 'peers');
  }

  private getManagerReview() {
    const manager = this.lookupManager();
  }

  private lookupManager() {
    return db.lookup(this.employee, 'manager');
  }

  private getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.review();
```

**[⬆ retour en haut](#sommaire)**

### Organiser les importations (imports)

Avec des instructions d'importation propres et faciles à lire, vous pouvez rapidement
voir les dépendances du code actuel. Assurez-vous d'appliquer les bonnes pratiques
suivantes pour les instructions `import`:

- Les déclarations d'importation doivent être classées par ordre alphabétique et regroupées.
- Les importations non utilisées doivent être supprimées.
- Les importations nommées doivent être classées par ordre alphabétique (c'est-à-dire `import {A, B, C} de 'foo';`)
- Les sources d'importation doivent être classées par ordre alphabétique dans les groupes, c'est-à-dire: `import * as foo from 'a'; importer *comme barre de 'b';`
- Les groupes d'importations sont délimités par des lignes blanches.
- Les groupes doivent respecter l'ordre suivant:
  - Polyfills (c'est-à-dire `import 'reflect-metadata';`)
  - modules intégrés de Node (c'est-à-dire `import fs from 'fs';`)
  - modules externes (c'est-à-dire `import {query} from 'itiriri';`)
  - modules internes (c'est-à-dire `import {UserService} from 'src/services/userService';`)
  - modules d'un répertoire parent (c'est-à-dire `import foo from '../foo'; import qux from '../../foo/qux';`)
  - modules du même répertoire ou d'un répertoire frère (c'est-à-dire `import bar from './bar'; import baz from './bar/baz';`)

**Mal:**

```ts
import { TypeDefinition } from '../types/typeDefinition';
import { AttributeTypes } from '../model/attribute';
import { ApiCredentials, Adapters } from './common/api/authorization';
import fs from 'fs';
import { ConfigPlugin } from './plugins/config/configPlugin';
import { BindingScopeEnum, Container } from 'inversify';
import 'reflect-metadata';
```

**Bien:**

```ts
import 'reflect-metadata';

import fs from 'fs';
import { BindingScopeEnum, Container } from 'inversify';

import { AttributeTypes } from '../model/attribute';
import { TypeDefinition } from '../types/typeDefinition';

import { ApiCredentials, Adapters } from './common/api/authorization';
import { ConfigPlugin } from './plugins/config/configPlugin';
```

**[⬆ retour en haut](#sommaire)**

### Utiliser des alias de TypeScript

Créez des importations plus jolies en définissant les chemins d'accès et les attributs
baseUrl dans la section `compilerOptions` dans le fichier `tsconfig.json`.

Cela évitera de longs chemins relatifs lors des importations.

**Mal:**

```ts
import { UserService } from '../../../services/UserService';
```

**Bien:**

```ts
import { UserService } from '@services/UserService';
```

```js
// tsconfig.json
...
  "compilerOptions": {
    ...
    "baseUrl": "src",
    "paths": {
      "@services": ["services/*"]
    }
    ...
  }
...
```

**[⬆ retour en haut](#sommaire)**

## Commentaires

L'utilisation de commentaires est une indication de non-expression sans eux.
Le code devrait être la seule source de vérité.

> Ne commentez pas le mauvais code - réécrivez-le.
> — *Brian W. Kernighan and P. J. Plaugher*

### Utiliser de préférence un code explicite plutôt que des commentaires

Les commentaires sont des excuses, pas une exigence. Un bon code *surtout* se documente.

**Mal:**

```ts
// Check if subscription is active.
if (subscription.endDate > Date.now) {  }
```

**Bien:**

```ts
const isSubscriptionActive = subscription.endDate > Date.now;
if (isSubscriptionActive) { /* ... */ }
```

**[⬆ retour en haut](#sommaire)**

### Ne pas laisser pas de code commenté dans votre base de code

Le contrôle de version existe pour une raison. Laissez l'ancien code dans votre historique.

**Mal:**

```ts
type User = {
  name: string;
  email: string;
  // age: number;
  // jobPosition: string;
}
```

**Bien:**

```ts
type User = {
  name: string;
  email: string;
}
```

**[⬆ retour en haut](#sommaire)**

### Ne pas avoir de commentaires dans l'archive de base

Rappelez-vous, utilisez le contrôle de version! Ce n'est pas nécessaire garder des
codes non utilisés ou commentés, et surtout de commentaires dans l'archive de base.
Utilisez le `git log` pour obtenir l'historique!

**Mal:**

```ts
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Added type-checking (LI)
 * 2015-03-14: Implemented combine (JR)
 */
function combine(a: number, b: number): number {
  return a + b;
}
```

**Bien:**

```ts
function combine(a: number, b: number): number {
  return a + b;
}
```

**[⬆ retour en haut](#sommaire)**

### Éviter les marqueurs de position

Ils ajoutent généralement du "noise" au code. Laissez les fonctions et les noms
de variables ainsi que l'indentation et le formatage appropriés donner la structure
visuelle à votre code.
La plupart des IDE prennent en charge la fonction de pliage de code qui vous permet
de réduire/développer des blocs de code (voir  Visual Studio Code
[régions de pliage](https://code.visualstudio.com/updates/v1_17#_folding-regions)).

**Mal:**

```ts
////////////////////////////////////////////////////////////////////////////////
// Client class
////////////////////////////////////////////////////////////////////////////////
class Client {
  id: number;
  name: string;
  address: Address;
  contact: Contact;

  ////////////////////////////////////////////////////////////////////////////////
  // public methods
  ////////////////////////////////////////////////////////////////////////////////
  public describe(): string {
    // ...
  }

  ////////////////////////////////////////////////////////////////////////////////
  // private methods
  ////////////////////////////////////////////////////////////////////////////////
  private describeAddress(): string {
    // ...
  }

  private describeContact(): string {
    // ...
  }
};
```

**Bien:**

```ts
class Client {
  id: number;
  name: string;
  address: Address;
  contact: Contact;

  public describe(): string {
    // ...
  }

  private describeAddress(): string {
    // ...
  }

  private describeContact(): string {
    // ...
  }
};
```

**[⬆ retour en haut](#sommaire)**

### Les commentaires TODO

Lorsque vous vous rendez compte que vous devez laisser des notes dans le code pour
des améliorations ultérieures, faites-le en utilisant les commentaires `// TODO`.
La plupart des IDE ont un support spécial pour ce genre de commentaires afin que
vous puissiez parcourir rapidement la liste complète des "todos".

Gardez cependant à l'esprit qu'un commentaire *TODO* n'est pas une excuse pour un mauvais code.

**Mal:**

```ts
function getActiveSubscriptions(): Promise<Subscription[]> {
  // ensure `dueDate` is indexed.
  return db.subscriptions.find({ dueDate: { $lte: new Date() } });
}
```

**Bien:**

```ts
function getActiveSubscriptions(): Promise<Subscription[]> {
  // TODO: ensure `dueDate` is indexed.
  return db.subscriptions.find({ dueDate: { $lte: new Date() } });
}
```

**[⬆ retour en haut](#sommaire)**

## Traductions

Ceci est également disponible dans d'autres langues:

- ![en](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/United-States.png) **Anglais**: [labs42io/clean-code-typescript](https://github.com/labs42io/clean-code-typescript)
- ![br](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Brazil.png) **Portugais Brésilien**: [vitorfreitas/clean-code-typescript](https://github.com/vitorfreitas/clean-code-typescript)
- ![cn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Chinois**:

  - [beginor/clean-code-typescript](https://github.com/beginor/clean-code-typescript)
  - [pipiliang/clean-code-typescript](https://github.com/pipiliang/clean-code-typescript)
- ![ja](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Japan.png) **Japonais**: [MSakamaki/clean-code-typescript](https://github.com/MSakamaki/clean-code-typescript)
- ![ru](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Russia.png) **Russe**: [Real001/clean-code-typescript](https://github.com/Real001/clean-code-typescript)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Spain.png) **Espagnol**: [JoseDeFreitas/clean-code-typescript](https://github.com/JoseDeFreitas/clean-code-typescript)
- ![tr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Turkey.png) **Turc**: [ozanhonamlioglu/clean-code-typescript](https://github.com/ozanhonamlioglu/clean-code-typescript)
- ![ko](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/South-Korea.png) **Coréen**: [738/clean-code-typescript](https://github.com/738/clean-code-typescript)

Les références seront ajoutées une fois les traductions terminées. Consultez cette
[discussion](https://github.com/labs42io/clean-code-typescript/issues/15)
pour plus de détails et de progrès. Vous pouvez apporter une contribution
indispensable à la communauté *Clean Code* en le traduisant dans votre langue.
