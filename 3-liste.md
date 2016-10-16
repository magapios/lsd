# Laboratorul 3 - implementarea parcurgerilor standard pentru liste; operații pe liste

## Afișarea listelor

### Afișarea listelor de numere întregi

Scrieți o funcție `print_list_of_ints` care primește o listă de numere întregi și o afișează la ieșirea standard.

*Rezolvare*

Pentru a **afișa o listă de numere întregi**
- nu facem nimic, dacă lista este goală;
- afișăm primul număr și apoi **afișăm restul listei**, dacă lista are cel puțin un număr.

Acest algoritm este ușor de implementat folosind [potriviri de tipare](http://caml.inria.fr/pub/docs/oreilly-book/html/book-ora016.html).

```ocaml
let rec print_list_of_ints xs = match xs with
    [] -> ()
  | h::t -> print_int h; print_list_of_ints t in
print_list_of_ints [1; 2]
```

> Cum orice expresie trebuie să producă o valoare iar funcția `print_list_of_ints` este apelată pentru [efectul ei secundar](https://en.wikipedia.org/wiki/Side_effect_(computer_science)) (în contrast cu funcțiile care produc valori utile care, în general, nu au efecte secundare) aceasta produce un `unit`; unica valoare a tipului `unit` este `()`, valoare pe care funcția `print_list_of_ints` o produce atunci când primește o listă goală și, deci, nu are nimic de făcut.

### Afișarea listelor de șiruri de caractere

Scrieți o funcție `print_list_of_strings` care primește o listă de șiruri de caractere și o afișează la ieșirea standard.

*Rezolvare*

Pentru a **afișa o listă de șiruri de caractere**
- nu facem nimic, dacă lista este goală;
- afișăm primul șir de caractere și apoi **afișăm restul listei**, dacă lista are cel puțin un șir de caractere.

La fel ca și algoritmul precedent, acest algoritm este ușor de implementat folosind potriviri de tipare.

```ocaml
let rec print_list_of_strings xs = match xs with
    [] -> ()
  | h::t -> print_string h; print_list_of_strings t in
print_list_of_strings ["ana"; "bob"]
```

### Generalizare

Cele două probleme prezentate reprezintă cazuri particulare ale unei probleme mai generale, aceea a apelării unei funcții (funcție apelată pentru efectul ei secundar ci nu pentru valoarea pe care o produce) pentru fiecare element al unei liste.

Putem scrie o funcție mai generală, `iter`, care primește pe lângă lista de parcurs și o acțiune `f` (o funcție de un element al listei pe care o apelăm pentru efectul ei secundar) și apelează acțiunea pentru fiecare element al listei. Funcția `iter` separă partea mecanică a parcurgerii listei (întotdeauna aceeași) de acțiunea care trebuie făcută cu fiecare element al listei (posibil diferită, în funcție de caz) și, deci, evită duplicarea codului pe care o putem observa implementările precedente.

```ocaml
let rec iter f xs = match xs with
    [] -> ()
  | h::t -> f h; iter f t in
iter print_int [1; 2];
iter print_string ["ana"; "bob"]
```

#### Îmbunătățiri

##### Evitarea trimiterii recursive a acțiunii

Cum acțiunea `f` este aceeași pentru toate apelurile generate de un apel al funcției `iter` nu trebuie să o trimitem la fiecare apel recursiv. Pentru aceasta, introducem o funcție locală `iter'` care implementează parcurgerea propriu-zisă și care, fiind definită în interiorul funcției `iter`, va putea să folosească acțiunea `f` primită de aceasta.

```ocaml
let iter f xs =
  let rec iter' xs = match xs with
      [] -> ()
    | h::t -> f h; iter' t in
  iter' xs in
iter print_int [1; 2];
iter print_string ["ana"; "bob"]
```

> Cum funcția `iter` este doar un [*wrapper*](https://en.wikipedia.org/wiki/Wrapper_function) pentru funcția `iter'` nu trebuie să fie marcată ca și recursivă.

##### Folosirea construcției `function`

Cum funcția `iter'` face potrivire de tipare pe ultimul parametru o putem rescrie folosind construcția `function` în locul construcției `match expr with`.

```ocaml
let iter f xs =
  let rec iter' = function
      [] -> ()
    | h::t -> f h; iter' t in
  iter' xs in
iter print_int [1; 2];
iter print_string ["ana"; "bob"]
```

##### Evitarea specificării redundante a parametrilor

Cum funcția `iter` este implementată ca și `let iter f xs = iter' xs` (funcția `iter'` fiind o funcție locală a sa) o putem rescrie ca și `let iter f = iter'`.

```ocaml
let iter f =
  let rec iter' = function
      [] -> ()
    | h::t -> f h; iter' t in
  iter' in
iter print_int [1; 2];
iter print_string ["ana"; "bob"]
```

#### Funcțiile de bibliotecă List.iter și Printf.printf

Fiind o parcurgere standard a listelor, funcția `iter` este disponibilă în biblioteca standard a limbajelor de programare funcțională (în OCaml, este funcția `iter` din modulul `List`). Putem rescrie cu ușurință afișarea listelor de numere întregi și respectiv șiruri de caractere astfel încât să folosim funcția de bibliotecă.

```ocaml
List.iter print_int [1; 2];
List.iter print_string ["ana"; "bob"]
```

Putem folosi și funcția `printf` din modulul `Printf` pentru a afișa elementele listei separate de un spațiu.

```ocaml
List.iter (Printf.printf "%d ") [1; 2];
List.iter (Printf.printf "%s ") ["ana"; "bob"]
```

## Filtrarea listelor

### Filtrarea numerelor pare

Scrieți o funcție `get_evens` care primește o listă de numere întregi și produce lista numerelor pare din această listă.

*Exemplu*

`get_evens [1; 2; 3; 4; 5; 6; 7; 8; 9; 10]` produce `[2; 4; 6; 8; 10]`

*Rezolvare*

**Lista numerelor pare dintr-o listă** este
- lista goală, dacă lista este goală;
- primul element urmat de **lista numerelor pare din restul listei**, dacă lista are cel puțin un element și primul element este par;
- **lista numerelor pare din restul listei**, dacă lista are cel puțin un element și primul element este impar.

Acest algoritm este ușor de implementat folosind potriviri de tipare și [santinele](http://caml.inria.fr/pub/docs/manual-caml-light/node4.2.html).

```ocaml
let rec get_evens = function
    [] -> []
  | h::t when h mod 2 = 0 -> h::get_evens t
  | _::t -> get_evens t in
print_list "%d " (get_evens [1; 2; 3; 4; 5; 6; 7; 8; 9; 10])
```

> Cum tiparele sunt încercate în ordine nu trebuie să mai verificăm, pentru ultimul tipar, dacă primul element al listei este impar: știm sigur că este din moment ce al doilea tipar nu s-a potrivit.

> Cum în ultimul tipar nu ne interesează valoarea primului element nu trebuie să-l mai legăm de un nume: folosim tiparul *wildcard* pentru primul element.

### Filtrarea pătratelor perfecte

Scrieți o funcție `get_squares` care primește o listă de numere naturale și produce lista pătratelor perfecte din această listă.

*Exemplu*

`get_squares [1; 2; 3; 4; 5; 6; 7; 8; 9; 10]` produce `[1; 4; 9]`

*Rezolvare*

**Lista pătratelor perfecte dintr-o listă** de numere naturale este
- lista goală, dacă lista este goală;
- primul element urmat de **lista pătratelor perfecte din restul listei**, dacă lista are cel puțin un element și primul element este pătrat perfect;
- **lista pătratelor perfecte din restul listei**, dacă lista are cel puțin un element și primul element nu este pătrat perfect.

La fel ca și algoritmul precedent, acest algoritm este ușor de implementat folosind potriviri de tipare și santinele. A verifica dacă un număr natural este pătrat perfect este, totuși, o idee mai complicat decât a verifica dacă un număr este par: introducem în acest sens funcția locală `is_square` pentru a separa clar algoritmul de parcurgere de detaliile legate de această verificare.

```ocaml
let rec get_squares =
  let is_square n =
    let int_square_root = int_of_float (sqrt (float_of_int n)) in
    int_square_root * int_square_root = n in
  function
    [] -> []
  | h::t when is_square h -> h::get_squares t
  | h::t -> get_squares t in
print_list "%d " (get_squares [1; 2; 3; 4; 5; 6; 7; 8; 9; 10])
```

### Generalizare

Cele două probleme prezentate reprezintă cazuri particulare ale unei probleme mai generale, aceea a filtrării elementelor care respectă o anumită condiție. Putem scrie o funcție mai generală, `filter`, care primește pe lângă lista de filtrat și un predicat (o funcție de un element al listei care produce un boolean) și produce lista elementelor din lista inițială pentru care predicatul produce valoarea adevărat.

```ocaml
let filter p =
  let rec filter' = function
      [] -> []
    | h::t when p h -> h::filter' t
    | _::t -> filter' t in
  filter'
```

## Temă

### *Run-length encoding*

#### Funcția `to_n`
Scrieți o funcție `to_n` care primește un număr natural nenul `n` și produce lista care conține, în ordine, numerele naturale de la `1` la `n`, fiecare număr `i` apărând de `i` ori.

**Exemplu**

`to_n 4` produce `[1; 2; 2; 3; 3; 3; 4; 4; 4; 4]`

#### Funcția `without_consecutive_dups` (opțional)

Scrieți o funcție `without_consecutive_dups` care primește o listă și produce lista în care toate secvențele de elemente consecutive egale ale listei au fost înlocuite cu un singur element.
Folosiți funcția `to_n` pentru a genera liste cu care să testați funcția `without_consecutive_dups`.

**Exemplu**

`without_consecutive_dups [1; 2; 2; 3; 2; 2; 1]` produce `[1; 2; 3; 2; 1]`

#### Funcția `run_length_encoding`

Scrieți o funcție `run_length_encoding` care primește o listă și produce lista în care toate secvențele de elemente consecutive egale au fost înlocuite cu o singură pereche în care prima valoare este lungimea secvenței și cea de-a doua este elementul repetat.
Folosiți funcția `to_n` pentru a genera liste cu care să testați funcția `run_length_encoding`.

**Exemplu**

`run_length_encoding [1; 1; 1; 1; 10; 10; 2; 3]` produce `[(4, 1); (2; 10); (1, 2); (1, 3)]`

> [*Run-length encoding*](http://www.fileformat.info/mirror/egff/ch09_03.htm) reprezintă o metodă foarte simplă și rapidă compresie, folosită printre altele pentru compresia imaginilor de tip [TIFF](http://www.fileformat.info/format/tiff/egff.htm) și [Windows BMP](http://www.fileformat.info/format/bmp/egff.htm).

### Funcția `take`

Scrieți o funcție `take` care primește un număr `n` și o listă și produce lista primelor `n` elemente ale listei.

**Exemplu**

`take 3 [10; 20; 30; 40; 50; 60; 70; 80; 90]` produce `[ 10; 20; 30]`

### Funcția `skip`

Scrieți o funcție `skip` care primește un număr `n` și o listă și produce lista fără primele `n` elemente ale listei.

**Exemplu**

`skip 3 [10; 20; 30; 40; 50; 60; 70; 80; 90]` produce `[40; 50; 60; 70; 80; 90]`
