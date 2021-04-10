---
layout: page
title: UML
subtitle: Atividade APSE
---

## 1.	O que é UML?

<div style="text-align: justify">UML (Unified Modeling Language) é uma linguagem de notação (um jeito de escrever, ilustrar, comunicar) para uso em projetos de sistemas.
Esta linguagem é expressa através de diagramas. Cada diagrama é composto por elementos (formas gráficas usadas para os desenhos) que possuem relação entre si.
Os diagramas da UML se dividem em dois grandes grupos: diagramas estruturais e diagramas comportamentais.</div><br>

## 2.	Quais são os diagramas comportamentais?

<div style="text-align: justify">Diagramas estruturais devem ser utilizados para especificar detalhes da estrutura do sistema (parte estática), por exemplo: classes, métodos, interfaces, namespaces, serviços, como componentes devem ser instalados, como deve ser a arquitetura do sistema, etc.</div><br>

## 3.	Quais são os diagramas estruturais? 

<div style="text-align: justify">Diagramas comportamentais devem ser utilizados para especificar detalhes do comportamento do sistema (parte dinâmica), por exemplo: como as funcionalidades devem funcionar, como um processo de negócio deve ser tratado pelo sistema e como componentes estruturais trocam mensagens e como respondem às chamada.</div><br>

## 4.	O que é o diagrama de casos de uso?

<div style="text-align: justify">O Diagrama de Caso de Uso serve para representar como os casos de uso interagem entre si no sistema e com os usuários (atores), ou seja, como as funcionalidades se relacionarão umas com as outras e como serão utilizadas pelo usuário, durante o uso do sistema.</div>

<center><img src="https://user-images.githubusercontent.com/57163905/114277989-0044fd00-9a04-11eb-8ced-7dcd473fd8a8.png" alt="drawing" style="width:300px; height:300px;"/></center>

## 5.	Quais são os elementos do diagrama de casos de uso?

No diagrama acima temos os três elementos que citamos:

**1 – Ator:** quem fará a execução do caso de uso (quem executará a funcionalidade que está especificada no caso de uso). Atores podem ser de dois tipos: humano e sistêmico.

**2 – Casos de uso:** são os fluxos (Principal, Alternativo e Exceção). Cada fluxo possui uma série de passos (steps), e uma lógica sequencial que demonstra, passo a passo, como o fluxo é executado.

**3 – Relacionamentos:** casos de uso relacionam entre si, ma especificação da UML são definidos alguns tipos de relacionamento para casos de uso, mas os três principais relacionamentos são: Inclusão (Include), Extensão (Extends) e Herança (Generalization).

## 6.	O que é o relacionamento extends?

<div style="text-align: justify">Quando o caso de uso B estende o caso de uso A, significa que quando o caso de uso A for executado o caso de uso B poderá (poderá – talvez não seja) ser executado também. A direção do relacionamento é do caso de uso extensor (aqui o caso de uso B) para o caso de uso estendido (aqui o caso de uso A).</div><br>

## 7.	o que é o relacionamento includes?

<div style="text-align: justify">Quando o caso de uso A “inclui” o caso de uso B, significa que sempre que o caso de uso A for executado o caso de uso B também será executado. A direção do relacionamento é do caso de uso que está incluindo para o caso de uso incluído.</div><br>

## Referências
#### ▶ <https://www.ateomomento.com.br/diagramas-uml/>
#### ▶ <https://www.ateomomento.com.br/o-que-e-caso-de-uso/>
#### ▶ <https://www.ateomomento.com.br/caso-de-uso-include-extend-e-generalizacao/>
