% ident_animal.pl: 
% Fecha: 12/02/20 

% Base de conocimiento 
% Proposiciones 
prop(1, 'es un canido', p).
prop(2, 'es grande', p).
prop(3, 'es pequeño', p).
prop(4, 'es doméstico', p).
prop(5, 'es fiel a su amo', p).
prop(6, 'es un felino', p).
prop(7, 'es un ave', p).
prop(8, 'habla', p).

% Intermedios 
prop(11, 'grande o pequeño', i). 

% Objetivos 
prop(101, 'es un PERRO.', i). 
prop(102, 'es un GATO.', i). 
prop(103, 'es una COTORRA.', i). 

% Reglas 
regla(1, o, [2, 3], 11, 1.0). 
regla(2, y, [1, 11, 4], 101, 0.6). 
regla(3, y, [5], 101, 0.8). 
regla(4, y, [6, 3, 4], 102, 0.8). 
regla(5, y, [7, 3, 8], 103, 1.0). 

clase(1, objetivos, [101, 102, 103]). 

:- discontiguous main/0.

main :- 
    nl, write('CONSULTA'), nl, nl, 
    write('Entre el ID de la prop. a investigar: '), read(I), 
    nl, nl, write('RESULTADO'), nl, 
    reportar_prop(I).

identificar_perro :-
    write('Consultando si es un perro...'), nl,
    (   regla(2, y, [1, 11, 4], 101, _)
    ->  write('El animal es un PERRO.')
    ;   write('No se puede confirmar que el animal sea un PERRO.'),
        nl).

identificar_gato :-
    write('Consultando si es un gato...'), nl,
    (   regla(4, y, [6, 3, 4], 102, _)
    ->  write('El animal es un GATO.')
    ;   write('No se puede confirmar que el animal sea un GATO.'),
        nl).

identificar_cotorra :-
    write('Consultando si es una cotorra...'), nl,
    (   regla(5, y, [7, 3, 8], 103, _)
    ->  write('El animal es una COTORRA.')
    ;   write('No se puede confirmar que el animal sea una COTORRA.'),
        nl).

consultar_propiedad :-
    write('Ingrese el ID de la propiedad a investigar: '), read(ID),
    (   prop(ID, Propiedad, _)
    ->  write('La propiedad es: '), write(Propiedad), nl
    ;   write('No se encontró la propiedad con ID: '), write(ID), nl).

main :-
    nl,
    write('CONSULTA'), nl,
    write('Seleccione la opción:'), nl,
    write('1. Identificar perro'), nl,
    write('2. Identificar gato'), nl,
    write('3. Identificar cotorra'), nl,
    write('4. Consultar propiedad'), nl,
    read(Opcion),
    (   Opcion = 1 -> identificar_perro;
        Opcion = 2 -> identificar_gato;
        Opcion = 3 -> identificar_cotorra;
        Opcion = 4 -> consultar_propiedad;
        Opcion \= 1, Opcion \= 2, Opcion \= 3, Opcion \= 4 -> write('Opción no válida.'), nl
    ).

reportar_prop(I) :- 
prop(I, Desc, _), 
write(Desc), nl.

borrar_pesos :-
    retractall(peso_prop(_, _)).

peso_prop(ID, W) :-
    findall(W1, (regla(_, _, Conds, ID, W1), maplist(peso_prop, Conds)), Ws),
    sum_list(Ws, W).
