---
layout: page
title: Atividade para 31-08
subtitle:  BDAE6 - Atividade
---

## 1) instr com a possibilidade de ocorrências de um determinado caracter: é possível, qual a sintaxe?

### Sintaxe do INSTR
![image](https://user-images.githubusercontent.com/57163905/131401843-a6a64027-0cf4-4b85-9b26-74faddfdac8d.png)

A função retorna um inteiro indicando a posição do caractere na string que é o primeiro caractere desta ocorrência. Possui quatros parâmetros:
- String
- Substring
- Posição:  um número inteiro diferente de zero que indica o caractere da string onde o banco de dados Oracle começa a pesquisa. Se for negativo, o Oracle conta de trás para frente a partir do final da string e, em seguida, pesquisa de trás para frente a partir da posição resultante.
- Ocorrência: é um número inteiro que indica qual ocorrência na string

```
SELECT INSTR ('TESTANTADO O INSTR', 'O', 1, 2) "Instring" 
FROM DUAL; 

Instring
----
12

```

## 2) construir uma select que traga o último nome de uma sequencia de caractes

```
select substr('Pedro Henrique Ton Pauletti', instr('Pedro Henrique Ton Pauletti',' ', -1) + 1) lastName
from   dual

LASTNAME
----
Pauletti

```
