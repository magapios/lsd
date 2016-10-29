# Laboratorul 5 - exerciții rezolvate

## Afișarea dicționarelor

Scrieți o funcție `print` care primește un dicționar cu chei de tip șir de caractere și valori de tip întreg și îl afișează la ieșirea standard.

*Exemplu*

Pentru dicționarul corespunzător listei de asociere `[ ("Mere", 4); ("Gutui", 5); ("Pere", 6) ]` funcția afișează `{ "Mere": 4, "Gutui": 5, "Pere": 6 }`.

*Rezolvare*

Pentru fiecare asociere din dicționar folosim `Printf.printf` pentru afișare. Dacă în cazul listelor folosim direct `List.iter` pentru iterare, în cazul dicționarelor trebuie să creăm mai întâi un modul în care tipul cheie al dicționarului este restricționat la șir de caractere și apoi să folosim funcția `iter` a acestui modul pentru iterare.

```ocaml
module M = Map.Make(String)

let print m =
  print_string "{ ";
  M.iter (Printf.printf "\"%s\": %d, ") m;
  print_string "\b\b }\n"
```

> După afișarea perechilor am afișat de două ori caracterul `\b` (*backspace*): acesta va muta cursorul două poziții înapoi, permițându-ne practic să scriem peste virgula afișată după ultima pereche. *Pentru ce dicționar de intrare această implementare nu va funcționa corect? O puteți repara?*

Evaluăm funcția pentru dicționarul corespunzător listei de asociere de mai sus. Pentru a construi dicționarul, plecăm de la dicționarul vid (`M.empty`) și adăugăm (folosind `M.add`), pe rând, fiecare dintre cele trei asocieri.

```ocaml
print (M.add "Gutui" 5 (M.add "Pere" 12 (M.add "Mere" 8 M.empty)))
```

Începând cu OCaml 4.01, putem folosi [operatorul `|>`](http://blog.shaynefletcher.org/2013/12/pipelining-with-operator-in-ocaml.html) pentru a rescrie construcția de mai sus într-o formă mai ușor de înțeles.

```ocaml
M.empty |> M.add "Mere" 4 |> M.add "Gutui" 5 |> M.add "Pere" 6 |> print
```

> Funcția afișează `{ "Gutui": 5, "Mere": 4, "Pere": 6 }` în loc de `{ "Mere": 4, "Gutui": 5, "Pere": 6 }`—*de ce*?

**Important**: Exercițiile următoare presupun că modulul `M` și funcția `print` sunt disponibile.

## Transformarea unei liste de asociere în dicționar

Scrieți o funcție `list_to_max_map` care primește o listă de asociere cu perechi de tip `string * int list` și produce dicționarul în care fiecare șir de caractere e asociat maximului listei.

*Exemplu*

`list_to_max_map [ ("Mere", [2; 4; 3; 1]); ("Pere", [8; 7; 5; 6]) ]` produce `{ "Mere": 4, "Pere": 8 }`

*Rezolvare*

Plecăm de la dicționarul vid și, pentru fiecare element de tip `string * int list` al listei, asociem prima valoare a elementului cu maximul celei de-a doua. În implementare putem folosi funcția `List.fold_left` pentru parcurgerea listei; pentru calcularea maximului putem defini o funcție locală `max_list` implementată tot folosind `List.fold_left`.

```ocaml
let list_to_max_map =
  let max_list = function
      [] -> invalid_arg "empty list"
    | h::t -> List.fold_left max h t in
  List.fold_left (fun m (k, xs) -> M.add k (max_list xs) m) M.empty
```

Apelăm funcția cu o listă de asociere și afișăm dicționarul rezultat folosind funcția `print`.

```ocaml
print (list_to_max_map [ ("Mere", [2; 4; 3; 1]); ("Pere", [8; 7; 5; 6]) ])
```

Putem rescrie, fără a folosi paranteze, folosind fie operatorul `@@` fie operatorul `|>`.

```ocaml
print @@ list_to_max_map [ ("Mere", [2; 4; 3; 1]); ("Pere", [8; 7; 5; 6]) ]
```

sau

```ocaml
[ ("Mere", [2; 4; 3; 1]); ("Pere", [8; 7; 5; 6]) ] |> list_to_max_map |> print
```

## Filtrarea dicționarelor

Scrieți o funcție `filter` care primește un predicat și un dicționar și produce dicționarul care conține acele asocieri din dicționarul inițial pentru care predicatul este adevărat.

*Rezolvare*

Plecăm de la un dicționar vid și, pentru fiecare asociere din dicționar (fiecare asociere constă într-o cheie și o valoare), adăugăm asocierea în noul dicționar dacă și numai dacă predicatul produce adevărat pentru cheia și valoarea asocierii. Folosim funcția `M.fold` pentru parcurgerea dicționarului inițial.

```ocaml
let filter p m = M.fold (fun k v m -> if p k v then M.add k v m else m) m M.empty
```

> Funcția `M.fold` primește trei parametri:
> - funcția de acumulare; această funcție primește, la rândul ei, cheia, valoarea și acumulatorul și produce noua valoare a acumulatorului;
> - dicționarul peste care iterează;
> - valoarea inițială a acumulatorului.

Apelăm funcția cu predicatul `fun k v -> String.length k  = v` (funcție care returnează adevărat dacă și numai dacă lungimea cheii este egală cu valoarea) și un dicționar și afișăm dicționarul rezultat folosind funcția `print`.

```ocaml
filter (fun k v -> String.length k  = v) (M.empty |> M.add "Mere" 4 |> M.add "Gutui" 5 |> M.add "Pere" 6) |> print
```

> În general, nu trebuie să implementați funcția `filter`: aceasta este disponibilă în modulul `M`. 
