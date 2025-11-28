# 6-Séance 6 : Préprocesseur Avancé et Macros  
## 2-Inclusions Conditionnelles et Attributs (C23)  
### 2-Introduction aux attributs C23 (`[[maybe_unused]]`, `[[fallthrough]]`)  

---

## Introduction  
La norme C23 introduit officiellement plusieurs **attributs standards** pour améliorer la clarté du code et les messages du compilateur. Parmi eux, `[[maybe_unused]]` et `[[fallthrough]]` sont destinés respectivement à marquer des variables ou fonctions potentiellement inutilisées, et pour documenter le comportement intentionnel des `switch` avec chute de cas (*fallthrough*).

---

## 1. Attribut `[[maybe_unused]]`  

### 1.1 But  
Indique que la déclaration (variable, fonction, type, ou autre entité) peut ne pas être utilisée sans générer d’avertissement de compilation.

Cela permet un code plus propre et sans warning, particulièrement utile dans des conditions de compilation conditionnelle où certaines variables sont utilisées selon le contexte.

### 1.2 Syntaxe  

```c
[[maybe_unused]] int x;
[[maybe_unused]] static void foo(void);
```

### 1.3 Exemple  

```c
#include <stdio.h>

[[maybe_unused]] static int valeur_non_utilisee;

void fonction() {
    int a = 5;
    // valeur_non_utilisee pas utilisée ici, pas d’avertissement généré
    printf("a = %d\n", a);
}
```

Sans l’attribut, la plupart des compilateurs (avec options d’avertissement activées) signaleraient `valeur_non_utilisee` comme variable non utilisée.

---

## 2. Attribut `[[fallthrough]]`  

### 2.1 But  
Indique explicitement qu'un **case** dans une instruction `switch` doit tomber (fall through) dans le case suivant, même si aucune instruction `break` ou autre contrôle d’interruption n’est présent.  

Cela prévient les warnings des compilateurs modernes sur un fallthrough souvent accidentel.

### 2.2 Syntaxe  

```c
switch(value) {
    case 1:
        // code
        [[fallthrough]];
    case 2:
        // code lorsque cas 1 tombe sur cas 2
        break;
}
```

### 2.3 Exemple  

```c
#include <stdio.h>

void test(int val) {
    switch(val) {
        case 1:
            printf("Cas 1\n");
            [[fallthrough]];
        case 2:
            printf("Cas 2\n");
            break;
        default:
            printf("Autre cas\n");
    }
}

int main() {
    test(1); // Affiche cas 1 puis cas 2
    test(2); // Affiche cas 2 uniquement
}
```

---

## 3. Diagramme Mermaid : cycle d’utilisation des attributs C23  

```mermaid
flowchart TD
    A[Déclaration variable ou fonction] --> B{Est-elle utilisée ?}
    B -- Oui --> C[Compilation normale]
    B -- Non --> D{[[maybe_unused]] ?}
    D -- Oui --> E[Pas d’avertissement]
    D -- Non --> F[Avertissement compilateur]

    G[Switch case sans break] --> H{[[fallthrough]] présent ?}
    H -- Oui --> I[Pas d’avertissement, intention claire]
    H -- Non --> J[Avertissement possible]
```

---

## 4. Avantages des attributs C23  

- **Réduction du bruit** dans les messages d’avertissement.  
- **Documentation implicite** des intentions du programmeur.  
- Amélioration de la **qualité et maintenance** du code.  
- Harmonisation avec les pratiques modernes des compilateurs.  

---

## 5. Compatibilité  

- Ces attributs sont repris dans **C++17** et versions ultérieures.  
- Support aujourd’hui dans GCC (depuis 9), Clang (depuis 7), MSVC (depuis Visual Studio 2019).  
- Utiliser `__has_cpp_attribute` pour vérifier leur disponibilité dans certains environnements.  

---

## 6. Sources utilisées  

- [ISO/IEC JTC1 SC22 WG14, Draft C23 standard](https://www.open-std.org/jtc1/sc22/wg14/www/docs/n3047.pdf)  
- [GCC Attributes](https://gcc.gnu.org/onlinedocs/gcc/Common-Function-Attributes.html)  
- [Clang Attributes Reference](https://clang.llvm.org/docs/LanguageExtensions.html#attributes)  
- [cppreference - [[maybe_unused]]](https://en.cppreference.com/w/c/language/attributes/maybe_unused)  
- [cppreference - [[fallthrough]]](https://en.cppreference.com/w/c/language/attributes/fallthrough)  

---

## Conclusion  
Avec `[[maybe_unused]]` et `[[fallthrough]]`, la norme C23 formalise des pratiques largement utilisées en C++ et présentes comme extensions compilateurs. Leur usage explicite aide à produire des programmes plus explicites et robustes en évitant les faux positifs dans les outils d’analyse statique et compilation.