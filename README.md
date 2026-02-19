Resolución de Ejercicios — Capítulo 1

Libro: flex & bison — flex & bison

Ejercicio 1

La calculadora del capítulo no acepta correctamente una línea que contenga únicamente un comentario si la gramática no contempla líneas vacías. Esto ocurre porque el parser está diseñado para reconocer expresiones seguidas de un salto de línea. Cuando el scanner ignora el comentario, el parser solo recibe el carácter de nueva línea. Si la gramática no permite una línea compuesta únicamente por \n, se genera un error sintáctico.

El problema no debe resolverse en el parser, ya que los comentarios pertenecen al análisis léxico. Lo más adecuado es modificarlos en el scanner para que sean ignorados completamente antes de que lleguen al parser.

La solución consiste en agregar en flex una regla como la siguiente:

"#".*    ;

De esta manera, cualquier comentario será eliminado durante el análisis léxico y no afectará la estructura sintáctica del programa.

Ejercicio 2

Para que la calculadora acepte números tanto decimales como hexadecimales, es necesario modificar el scanner. Un número hexadecimal en C comienza con el prefijo 0x o 0X, seguido de dígitos del 0 al 9 o letras entre A y F.

La expresión regular adecuada en flex es:

0[xX][0-9a-fA-F]+

Para convertir correctamente el valor reconocido, se utiliza la función strtol, permitiendo que el propio sistema determine la base numérica automáticamente:

0[xX][0-9a-fA-F]+ {
    yylval = strtol(yytext, NULL, 0);
    return NUMBER;
}

[0-9]+ {
    yylval = atoi(yytext);
    return NUMBER;
}

En el parser se puede modificar la acción semántica para imprimir el resultado en decimal y hexadecimal:

| exp '\n' {
    printf("Decimal: %d\n", $1);
    printf("Hexadecimal: 0x%X\n", $1);
}

Con esto, la calculadora puede procesar expresiones como 0x10 + 10 y mostrar el resultado en ambas bases numéricas.

Ejercicio 3

Para agregar los operadores AND (&) y OR (|) bit a bit, se debe extender tanto el scanner como el parser.

En el scanner se agregan las reglas:

"&"  return '&';
"|"  return '|';

En el parser es necesario definir la precedencia de los operadores para evitar ambigüedades:

%left '|'
%left '&'

Luego se incorporan las reglas semánticas correspondientes:

| exp '&' exp { $$ = $1 & $3; }
| exp '|' exp { $$ = $1 | $3; }

Con estas modificaciones la calculadora puede evaluar expresiones con operaciones bit a bit respetando la precedencia establecida.

Ejercicio 4

El scanner escrito manualmente no necesariamente reconoce exactamente los mismos tokens que el scanner generado por flex. Esto se debe a que flex construye internamente un autómata finito determinista a partir de expresiones regulares formales y aplica el principio de máxima coincidencia. En un scanner manual es posible cometer errores en el manejo de estados, coincidencias parciales o reconocimiento de caracteres especiales.

Aunque ambos puedan funcionar correctamente en la mayoría de los casos, pueden comportarse de manera diferente en situaciones límite, especialmente ante entradas ambiguas o caracteres inesperados.

Ejercicio 5

Flex no es una herramienta adecuada para todos los lenguajes. Está basado en autómatas finitos y por lo tanto no maneja dependencias complejas de contexto. No resulta ideal para lenguajes donde la indentación determina la estructura sintáctica, como ocurre en Python, ya que en estos casos el análisis léxico depende del nivel de anidación y del contexto previo.

Tampoco es apropiado cuando el análisis léxico requiere información sintáctica avanzada o dependencias que superen la capacidad de un autómata finito. En esos casos, se necesitarían herramientas más sofisticadas o integración estrecha entre el lexer y el parser.

Ejercicio 6

Reescribir el programa de conteo de palabras en C puro implica manejar manualmente el flujo de caracteres utilizando funciones como getchar() y controlar estados internos para determinar cuándo comienza o termina una palabra. El programador debe implementar explícitamente la lógica para contar líneas, palabras y caracteres.

Aunque una implementación manual puede resultar ligeramente más eficiente en términos de rendimiento, también es más propensa a errores y más difícil de mantener. Flex, en cambio, automatiza la construcción del autómata y permite expresar el comportamiento mediante expresiones regulares claras y estructuradas.

En términos prácticos, la diferencia de rendimiento suele ser mínima, mientras que la claridad y mantenibilidad del código generado con flex representan una ventaja considerable.
