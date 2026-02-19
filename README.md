Ejercicio 1
❓ Enunciado

¿Acepta la calculadora una línea que contenga solo un comentario?
¿Por qué?
¿Es más fácil arreglarlo en el scanner o en el parser?

🔎 Análisis Paso a Paso
1️⃣ Cómo funciona la gramática original

La calculadora del capítulo tiene una regla similar a:

line:
      '\n'
    | exp '\n' { printf("Resultado: %d\n", $1); }

El parser solo acepta:

Una expresión seguida de salto de línea.

O una línea vacía.

2️⃣ ¿Qué pasa con un comentario?

Si escribimos:

# esto es un comentario

Y el scanner ignora el comentario, el parser solo recibe:

\n

Si la gramática no acepta líneas vacías → se produce error sintáctico.

3️⃣ ¿Dónde es mejor arreglarlo?

Es más correcto arreglarlo en el scanner (flex), porque:

Los comentarios pertenecen al análisis léxico.

El parser no debería procesarlos.

✔ Solución en Flex
"#".*    ;

Esto ignora completamente los comentarios.

✅ Conclusión

Es más limpio y correcto manejar los comentarios en el scanner, no en el parser.

✅ Ejercicio 2
❓ Enunciado

Modificar la calculadora para que acepte números decimales y hexadecimales.

🔎 Paso 1 — Reconocer números hexadecimales

Formato hexadecimal:

0x1A3F

Expresión regular:

0[xX][0-9a-fA-F]+
🔎 Paso 2 — Convertir correctamente el valor

Usamos:

strtol(yytext, NULL, 0);

El parámetro 0 permite detectar automáticamente si es base 10 o base 16.

🔎 Paso 3 — Scanner modificado
0[xX][0-9a-fA-F]+ {
    yylval = strtol(yytext, NULL, 0);
    return NUMBER;
}

[0-9]+ {
    yylval = atoi(yytext);
    return NUMBER;
}
🔎 Paso 4 — Mostrar resultado en ambas bases

En el parser:

| exp '\n' {
    printf("Decimal: %d\n", $1);
    printf("Hexadecimal: 0x%X\n", $1);
}
✅ Resultado

Entrada:

0x10 + 10

Salida:

Decimal: 26
Hexadecimal: 0x1A
✅ Ejercicio 3 (Extra Credit)
❓ Enunciado

Agregar operadores AND (&) y OR (|) bit a bit.

🔎 Paso 1 — Scanner
"&"  return '&';
"|"  return '|';
🔎 Paso 2 — Definir precedencia

En Bison:

%left '|'
%left '&'
