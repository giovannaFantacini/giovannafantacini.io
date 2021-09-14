---
layout: page
title: Pesquisa 14/09
subtitle:  BDAE62 - Atividade
---


1- Quantidade de resíduos entregues por data/tipo de resíduo, mostrando a data, o tipo de resíduo e a quantidade total;<br>
2- Quantidade de resíduos entregues por data/ponto de coleta, mostrando a data, o ponto de coleta e a quantidade total;<br>
3- Quantidade de resíduos entregues por data/cidadão, mostrando a data, o cidadão e a quantidade total;<br>
4- Quantidade de resíduos entregues por data/bairro, mostrando a data, o bairro e a quantidade total;<br>
5- Quantidade de resíduos entregues por data/estado, mostrando a data, o estado e a quantidade total;<br>
6- Quantidade de resíduos entregues por data/cidade, mostrando a data, a cidade e a quantidade total;<br>
7- Quantidade de resíduos entregues por tipo de resíduo, mostrando o tipo de resíduo e a quantidade total;<br>
8- Quantidade de resíduos entregues por ponto de coleta, mostrando o ponto de coleta e quantidade total;<br>
9- Quantidade de resíduos entregues por cidadão, mostrando o cidadão e quantidade total;<br>
10- Quantidade de resíduos entregues por bairro, mostrando o bairro e quantidade total;<br>
11- Quantidade de resíduos entregues por estado, mostrando o estado e quantidade total;<br>
12- Quantidade de resíduos entregues por cidade, mostrando a cidade e quantidade total;<br>
13- Quantidade de resíduos por mês em um tipo de resíduo, mostrando mês e quantidade total;<br>
14- Quantidade de resíduos por mês em um ponto de coleta, mostrando mês e quantidade total;<br>
15- Quantidade de resíduos por mês de um cidadão, mostrando mês e quantidade total;<br>
16- Quantidade de resíduos por mês em um bairro, mostrando mês e quantidade total;<br>
17- Quantidade de resíduos por mês em um estado, mostrando mês e quantidade total;<br>
18- Quantidade de resíduos por mês em uma cidade, mostrando mês e quantidade total;<br>
19- Quantidade de resíduos por ano em um tipo de resíduo, mostrando ano e quantidade total;<br>
20- Quantidade de resíduos por ano em um ponto de coleta, mostrando ano e quantidade total;<br>
21- Quantidade de resíduos por ano de um cidadão, mostrando ano e quantidade total;<br>
22- Quantidade de resíduos por ano em um bairro, mostrando ano e quantidade total;<br>
23- Quantidade de resíduos por ano em um estado, mostrando ano e quantidade total;<br>
24- Quantidade de resíduos por ano em uma cidade, mostrando ano e quantidade total;<br>



```
--1
SELECT DATAENTREGA,TIPORESIDUO.NOME,QUANTIDADE
FROM   RESIDUO, TIPORESIDUO 
WHERE RESIDUO.ID_TIPORESIDUO = TIPORESIDUO.ID
 
SELECT * FROM RESIDUO

 
SELECT DATAENTREGA,TIPORESIDUO.NOME,COUNT(QUANTIDADE),SUM(QUANTIDADE)
FROM   RESIDUO, TIPORESIDUO 
WHERE RESIDUO.ID_TIPORESIDUO = TIPORESIDUO.ID 
GROUP BY DATAENTREGA, TIPORESIDUO.NOME
 
SELECT DATAENTREGA,TIPORESIDUO.NOME, UNIDADEMEDIDA.NOME, COUNT(QUANTIDADE),SUM(QUANTIDADE)
FROM   RESIDUO, TIPORESIDUO, UNIDADEMEDIDA
WHERE RESIDUO.ID_TIPORESIDUO = TIPORESIDUO.ID 
AND TIPORESIDUO.ID_UNIDADEMEDIDA = UNIDADEMEDIDA.ID
GROUP BY DATAENTREGA, TIPORESIDUO.NOME, UNIDADEMEDIDA.NOME


--2
SELECT DATAENTREGA,PONTOCOLETA.NOME,COUNT(QUANTIDADE),SUM(QUANTIDADE)
FROM   RESIDUO, PONTOCOLETA 
WHERE  RESIDUO.ID_PONTOCOLETA = PONTOCOLETA.ID 
GROUP BY DATAENTREGA, PONTOCOLETA.NOME

--3
SELECT DATAENTREGA, CIDADAO.PRIMEIRONOME, CIDADAO.NOMEMEIO, CIDADAO.ULTIMONOME, SUM(QUANTIDADE), COUNT(QUANTIDADE)
FROM RESIDUO, CIDADAO
WHERE RESIDUO.IDCIDADAO = CIDADAO.ID
GROUP BY DATAENTREGA, CIDADAO.PRIMEIRONOME, CIDADAO.NOMEMEIO, CIDADAO.ULTIMONOME
 
SELECT DATAENTREGA, CIDADAO.PRIMEIRONOME || ' ' || CIDADAO.NOMEMEIO ||  ' ' || CIDADAO.ULTIMONOME, SUM(QUANTIDADE), COUNT(QUANTIDADE)
FROM RESIDUO, CIDADAO
WHERE RESIDUO.IDCIDADAO = CIDADAO.ID
GROUP BY DATAENTREGA, CIDADAO.PRIMEIRONOME, CIDADAO.NOMEMEIO, CIDADAO.ULTIMONOME
 
INSERT INTO RESIDUO (DATAENTREGA, QUANTIDADE, IDCIDADAO, IDPONTOCOLETA, IDTIPORESIDUO)
VALUES ('03/02/2021', 15, 3, 1, 6)

--4
SELECT DATAENTREGA, BAIRRO.NOME AS BAIRRO, SUM(QUANTIDADE)
FROM RESIDUO, PONTOCOLETA, BAIRRO
WHERE RESIDUO.IDPONTOCOLETA = PONTOCOLETA.ID AND PONTOCOLETA.IDBAIRRO = BAIRRO.ID
GROUP BY DATAENTREGA, BAIRRO.NOME

--5
SELECT DATAENTREGA, ESTADOPAIS.NOME, SUM(QUANTIDADE)
FROM RESIDUO, CIDADAO, BAIRRO, CIDADE, ESTADOPAIS
WHERE RESIDUO.IDCIDADAO = CIDADAO.ID AND CIDADAO.IDBAIRRO = BAIRRO.ID AND BAIRRO.IDCIDADE = CIDADE.ID AND CIDADE.IDESTADOPAIS = ESTADOPAIS.ID
GROUP BY DATAENTREGA, ESTADOPAIS.NOME

--6
SELECT DATAENTREGA, CIDADE.NOME AS CIDADE, SUM(QUANTIDADE)
FROM RESIDUO, PONTOCOLETA, BAIRRO, CIDADE
WHERE RESIDUO.IDPONTOCOLETA = PONTOCOLETA.ID AND PONTOCOLETA.IDBAIRRO = BAIRRO.ID AND BAIRRO.IDCIDADE = CIDADE.ID
GROUP BY DATAENTREGA, CIDADE.NOME

```
