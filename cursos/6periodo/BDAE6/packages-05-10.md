---
layout: page
title: Packages 05/10
subtitle:  BDAE62 - Atividade
---

---


## PKG_ESTADOPAIS

### Specification
```
create or replace PACKAGE PKG_ESTADOPAIS AS 
vr_estadoPais   estadoPais%ROWTYPE;
PROCEDURE PR_INCLUIR        ( pr_estadoPais  IN  estadoPais%ROWTYPE,
                               pv_erro        OUT varchar2
                            );
PROCEDURE PR_ALTERAR         ( pr_estadoPais  IN  estadoPais%ROWTYPE );
PROCEDURE PR_EXCLUIR         ( pn_id          IN  number );
FUNCTION  FN_BUSCAR          ( pn_id          IN  number )   return estadoPais%ROWTYPE;
FUNCTION  FN_BUSCAR          ( pv_conteudo    IN  varchar2,
                               pn_tipoBusca   IN  number
                             ) return estadoPais%ROWTYPE;
FUNCTION  FN_VERIFICAREXISTE ( pv_conteudo    IN  varchar2,
                               pn_tipoBusca   IN  number
                             ) return boolean;
END PKG_ESTADOPAIS;
```

### Body
```
create or replace PACKAGE BODY PKG_ESTADOPAIS AS

--------------------------procedure para incluir dados ---------------------------------------
PROCEDURE PR_INCLUIR ( pr_estadoPais  IN  estadoPais%ROWTYPE,
                         pv_erro        OUT varchar2
                       ) AS
  
  vb_existe   boolean;
  
  vn_id       number;
  
  BEGIN
    pv_erro   := '';

    
    --verifica se os dados informados não são nulos
    if      pr_estadoPais.nome is null then
            pv_erro := 'Nome não foi informado';
    else if pr_estadoPais.sigla is null then
            pv_erro := 'Sigla não foi informada';
        end if;
    end  if; 
    

    vb_existe := FN_VERIFICAREXISTE ( pv_conteudo    => pr_estadoPais.nome,
                                      pn_tipoBusca   => 1
                                      );

    if   vb_existe then
         pv_erro := 'Estado já cadastrado para o nome informado';
    else vb_existe := FN_VERIFICAREXISTE ( pv_conteudo    => pr_estadoPais.sigla,
                                           pn_tipoBusca   => 2
                                          );
         if   vb_existe then
              pv_erro := 'Estado já cadastrado para a sigla informada';
         end  if;
    end  if;
    
    --inclusao
    if   pv_erro is null then
         select ESTADOPAIS_SEQ.nextval
         into   vn_id
         from   dual;
    
         insert into estadoPais (id,    nome,               sigla)
                values          (vn_id, pr_estadoPais.nome, pr_estadoPais.sigla);
    end  if;

END PR_INCLUIR;

---------------------------------------------------------------------------------------



-------------------------procedure alterar dados---------------------------------------
  PROCEDURE PR_ALTERAR ( pr_estadoPais  IN  estadoPais%ROWTYPE ) AS
  BEGIN
    update estadoPais
    set    nome  = pr_estadoPais.nome,
           sigla = pr_estadoPais.sigla
    where  id    = pr_estadoPais.id;
  END PR_ALTERAR;
---------------------------------------------------------------------------------------




-------------------------procedure excluir dados---------------------------------------

  PROCEDURE PR_EXCLUIR ( pn_id          IN  number ) AS
  BEGIN
    delete from estadoPais
    where  id = pn_id;
  END PR_EXCLUIR;
---------------------------------------------------------------------------------------




---------------------------função buscar pelo id---------------------------------------
  FUNCTION  FN_BUSCAR  ( pn_id          IN  number ) return estadoPais%ROWTYPE is
  
  vr_estadoPais   EstadoPais%ROWTYPE;
  
  cursor Busca_EstadoPais is
  select id, nome, sigla
  from   estadoPais
  where  id = pn_id;
  
  BEGIN
    open   Busca_EstadoPais;
    fetch  Busca_EstadoPais into vr_estadoPais.id, vr_estadoPais.nome, vr_estadoPais.sigla;
    if     Busca_EstadoPais%NotFound then
           vr_estadoPais := null;
    end    if;
    close  Busca_EstadoPais;
    
    RETURN vr_estadoPais;
  END FN_BUSCAR;
-------------------------------------------------------------------------------------- 


-----------------------------função para buscar dado----------------------------------
  FUNCTION  FN_BUSCAR  ( pv_conteudo    IN  varchar2,
                         pn_tipoBusca   IN  number
                       ) return estadoPais%ROWTYPE is
   
  /*
  pn_tipoBusca
  1: busca pelo nome
  2: busca pela sigla
  */
  
  vr_estadoPais   EstadoPais%ROWTYPE;
  
  cursor Busca_EstadoPaisNome is
  select id, nome, sigla
  from   estadoPais
  where  upper(nome) = upper(pv_conteudo);
  
  cursor Busca_EstadoPaisSigla is
  select id, nome, sigla
  from   estadoPais
  where  upper(sigla) = upper(pv_conteudo);
  
  BEGIN
    if     pn_tipoBusca = 1 then
           open   Busca_EstadoPaisNome;
           fetch  Busca_EstadoPaisNome into vr_estadoPais.id, vr_estadoPais.nome, vr_estadoPais.sigla;
           if     Busca_EstadoPaisNome%NotFound then
                  vr_estadoPais := null;
           end    if;
           close  Busca_EstadoPaisNome;
    end    if;
    
    if     pn_tipoBusca = 2 then
           open   Busca_EstadoPaisSigla;
           fetch  Busca_EstadoPaisSigla into vr_estadoPais.id, vr_estadoPais.nome, vr_estadoPais.sigla;
           if     Busca_EstadoPaisSigla%NotFound then
                  vr_estadoPais := null;
           end    if;
           close  Busca_EstadoPaisSigla;
    end    if;
    
    RETURN vr_estadoPais;
  END FN_BUSCAR;
-------------------------------------------------------------------------------------- 



-------------------verifica se dado ja foi cadastrado--------------------------------- 
  FUNCTION  FN_VERIFICAREXISTE ( pv_conteudo    IN  varchar2,
                                 pn_tipoBusca   IN  number
                               ) return boolean is
  
  /*
  pn_tipoBusca
  1: busca pelo nome
  2: busca pela sigla
  */
  
  vb_existe  boolean;
  
  vn_qtde    number;
  
  cursor Busca_EstadoPaisNome is
  select count(*)
  from   estadoPais
  where  upper(nome) = upper(pv_conteudo);
  
  cursor Busca_EstadoPaisSigla is
  select count(*)
  from   estadoPais
  where  upper(sigla) = upper(pv_conteudo);
  
  BEGIN
    if     pn_tipoBusca = 1 then
           open  Busca_EstadoPaisNome;
           fetch Busca_EstadoPaisNome into vn_qtde;
           if    Busca_EstadoPaisNome%NotFound then
                 vn_qtde := 0;
           end   if;
           close Busca_EstadoPaisNome;
    else   open  Busca_EstadoPaisSigla;
           fetch Busca_EstadoPaisSigla into vn_qtde;
           if    Busca_EstadoPaisSigla%NotFound then
                 vn_qtde := 0;
           end   if;
           close Busca_EstadoPaisSigla;
    end    if;
         
    if     vn_qtde = 0 then
           vb_existe := false;
    else   vb_existe := true;
    end    if;
    
    return vb_existe;
  END FN_VERIFICAREXISTE;
-------------------------------------------------------------------------------------- 


END PKG_ESTADOPAIS;


```

## PKG_CIDADE

### Specification
```
create or replace PACKAGE PKG_CIDADE AS 
vr_cidade   cidade%ROWTYPE;
PROCEDURE PR_INCLUIR        ( pr_cidade  IN  cidade%ROWTYPE,
                               pv_erro        OUT varchar2
                            );
PROCEDURE PR_ALTERAR         ( pr_cidade  IN  cidade%ROWTYPE );
PROCEDURE PR_EXCLUIR         ( pn_id          IN  number );
FUNCTION  FN_BUSCAR          ( pn_id          IN  number )   return cidade%ROWTYPE;
FUNCTION  FN_BUSCAR          ( pv_conteudo    IN  varchar2,
                               pn_tipoBusca   IN  number
                             ) return cidade%ROWTYPE;
FUNCTION  FN_VERIFICAREXISTE ( pv_conteudo    IN  varchar2,
                               pn_tipoBusca   IN  number
                             ) return boolean;
END PKG_CIDADE;
```

### Body
```
create or replace PACKAGE BODY PKG_CIDADE AS

--------------------------procedure para incluir dados ---------------------------------------
PROCEDURE PR_INCLUIR ( pr_cidade  IN  cidade%ROWTYPE,
                         pv_erro        OUT varchar2
                       ) AS
  
  vb_existe   boolean;
  
  vn_id       number;
  
  BEGIN
    pv_erro   := '';

    
    --verifica se os dados informados não são nulos
    if      pr_cidade.nome is null then
            pv_erro := 'Nome não foi informado';
    end  if; 
    

    vb_existe := FN_VERIFICAREXISTE ( pv_conteudo    => pr_cidade.nome,
                                      pn_tipoBusca   => 1
                                      );

    if   vb_existe then
         pv_erro := 'Cidade já cadastrado para o nome informado';
    end  if;
    
    --inclusao
    if   pv_erro is null then
         select CIDADE_SEQ.nextval
         into   vn_id
         from   dual;
    
         insert into cidade (id,    nome,               idestadopais)
                values          (vn_id, pr_cidade.nome, pr_cidade.idestadopais);
    end  if;

END PR_INCLUIR;

---------------------------------------------------------------------------------------



-------------------------procedure alterar dados---------------------------------------
  PROCEDURE PR_ALTERAR ( pr_cidade  IN  cidade%ROWTYPE ) AS
  BEGIN
    update cidade
    set    nome  = pr_cidade.nome,
           idestadopais = pr_cidade.idestadopais
    where  id    = pr_cidade.id;
  END PR_ALTERAR;
---------------------------------------------------------------------------------------




-------------------------procedure excluir dados---------------------------------------

  PROCEDURE PR_EXCLUIR ( pn_id          IN  number ) AS
  BEGIN
    delete from cidade
    where  id = pn_id;
  END PR_EXCLUIR;
---------------------------------------------------------------------------------------




---------------------------função buscar pelo id---------------------------------------
  FUNCTION  FN_BUSCAR  ( pn_id          IN  number ) return cidade%ROWTYPE is
  
  vr_cidade   cidade%ROWTYPE;
  
  cursor Busca_cidade is
  select id, nome, idestadopais
  from   cidade
  where  id = pn_id;
  
  BEGIN
    open   Busca_cidade;
    fetch  Busca_cidade into vr_cidade.id, vr_cidade.nome, vr_cidade.idestadopais;
    if     Busca_cidade%NotFound then
           vr_cidade := null;
    end    if;
    close  Busca_cidade;
    
    RETURN vr_cidade;
  END FN_BUSCAR;
-------------------------------------------------------------------------------------- 


-----------------------------função para buscar dado----------------------------------
  FUNCTION  FN_BUSCAR  ( pv_conteudo    IN  varchar2,
                         pn_tipoBusca   IN  number
                       ) return cidade%ROWTYPE is
   
  /*
  pn_tipoBusca
  1: busca pelo nome
  */
  
  vr_cidade   cidade%ROWTYPE;
  
  cursor Busca_cidadeNome is
  select id, nome, idestadopais
  from   cidade
  where  upper(nome) = upper(pv_conteudo);

  BEGIN
    if     pn_tipoBusca = 1 then
           open   Busca_cidadeNome;
           fetch  Busca_cidadeNome into vr_cidade.id, vr_cidade.nome, vr_cidade.idestadopais;
           if     Busca_cidadeNome%NotFound then
                  vr_cidade := null;
           end    if;
           close  Busca_cidadeNome;
    end    if;
    
    
    RETURN vr_cidade;
  END FN_BUSCAR;
-------------------------------------------------------------------------------------- 



-------------------verifica se dado ja foi cadastrado--------------------------------- 
  FUNCTION  FN_VERIFICAREXISTE ( pv_conteudo    IN  varchar2,
                                 pn_tipoBusca   IN  number
                               ) return boolean is
  
  /*
  pn_tipoBusca
  1: busca pelo nome
  */
  
  vb_existe  boolean;
  
  vn_qtde    number;
  
  cursor Busca_cidadeNome is
  select count(*)
  from   cidade
  where  upper(nome) = upper(pv_conteudo);
  
  
  BEGIN
    if     pn_tipoBusca = 1 then
           open  Busca_cidadeNome;
           fetch Busca_cidadeNome into vn_qtde;
           if    Busca_cidadeNome%NotFound then
                 vn_qtde := 0;
           end   if;
           close Busca_cidadeNome;
    end    if;
         
    if     vn_qtde = 0 then
           vb_existe := false;
    else   vb_existe := true;
    end    if;
    
    return vb_existe;
  END FN_VERIFICAREXISTE;
-------------------------------------------------------------------------------------- 
END PKG_CIDADE;
```

## PKG_BAIRRO

### Specification
```
create or replace PACKAGE PKG_BAIRRO AS 
vr_bairro   bairro%ROWTYPE;
PROCEDURE PR_INCLUIR        ( pr_bairro  IN  bairro%ROWTYPE,
                               pv_erro        OUT varchar2
                            );
PROCEDURE PR_ALTERAR         ( pr_bairro  IN  bairro%ROWTYPE );
PROCEDURE PR_EXCLUIR         ( pn_id          IN  number );
FUNCTION  FN_BUSCAR          ( pn_id          IN  number )   return bairro%ROWTYPE;
FUNCTION  FN_BUSCAR          ( pv_conteudo    IN  varchar2,
                               pn_tipoBusca   IN  number
                             ) return bairro%ROWTYPE;
FUNCTION  FN_VERIFICAREXISTE ( pv_conteudo    IN  varchar2,
                               pn_tipoBusca   IN  number
                             ) return boolean;
END PKG_BAIRRO;

```

### Body
```
create or replace PACKAGE BODY PKG_BAIRRO AS

--------------------------procedure para incluir dados ---------------------------------------
PROCEDURE PR_INCLUIR ( pr_bairro  IN  bairro%ROWTYPE,
                         pv_erro        OUT varchar2
                       ) AS
  
  vb_existe   boolean;
  
  vn_id       number;
  
  BEGIN
    pv_erro   := '';

    
    --verifica se os dados informados não são nulos
    if      pr_bairro.nome is null then
            pv_erro := 'Nome não foi informado';
    end  if; 
    

    vb_existe := FN_VERIFICAREXISTE ( pv_conteudo    => pr_bairro.nome,
                                      pn_tipoBusca   => 1
                                      );

    if   vb_existe then
         pv_erro := 'Bairro já cadastrado para o nome informado';
    end  if;
    
    --inclusao
    if   pv_erro is null then
         select BAIRRO_SEQ.nextval
         into   vn_id
         from   dual;
    
         insert into bairro (id,    nome,               idcidade)
                values          (vn_id, pr_bairro.nome, pr_bairro.idcidade);
    end  if;

END PR_INCLUIR;

---------------------------------------------------------------------------------------



-------------------------procedure alterar dados---------------------------------------
  PROCEDURE PR_ALTERAR ( pr_bairro  IN  bairro%ROWTYPE ) AS
  BEGIN
    update bairro
    set    nome  = pr_bairro.nome,
           idcidade = pr_bairro.idcidade
    where  id    = pr_bairro.id;
  END PR_ALTERAR;
---------------------------------------------------------------------------------------




-------------------------procedure excluir dados---------------------------------------

  PROCEDURE PR_EXCLUIR ( pn_id          IN  number ) AS
  BEGIN
    delete from bairro
    where  id = pn_id;
  END PR_EXCLUIR;
---------------------------------------------------------------------------------------




---------------------------função buscar pelo id---------------------------------------
  FUNCTION  FN_BUSCAR  ( pn_id          IN  number ) return bairro%ROWTYPE is
  
  vr_bairro   bairro%ROWTYPE;
  
  cursor Busca_bairro is
  select id, nome, idcidade
  from   bairro
  where  id = pn_id;
  
  BEGIN
    open   Busca_bairro;
    fetch  Busca_bairro into vr_bairro.id, vr_bairro.nome, vr_bairro.idcidade;
    if     Busca_bairro%NotFound then
           vr_bairro := null;
    end    if;
    close  Busca_bairro;
    
    RETURN vr_bairro;
  END FN_BUSCAR;
-------------------------------------------------------------------------------------- 


-----------------------------função para buscar dado----------------------------------
  FUNCTION  FN_BUSCAR  ( pv_conteudo    IN  varchar2,
                         pn_tipoBusca   IN  number
                       ) return bairro%ROWTYPE is
   
  /*
  pn_tipoBusca
  1: busca pelo nome
  */
  
  vr_bairro   bairro%ROWTYPE;
  
  cursor Busca_bairroNome is
  select id, nome, idcidade
  from   bairro
  where  upper(nome) = upper(pv_conteudo);

  BEGIN
    if     pn_tipoBusca = 1 then
           open   Busca_bairroNome;
           fetch  Busca_bairroNome into vr_bairro.id, vr_bairro.nome, vr_bairro.idcidade;
           if     Busca_bairroNome%NotFound then
                  vr_bairro := null;
           end    if;
           close  Busca_bairroNome;
    end    if;
    
    
    RETURN vr_bairro;
  END FN_BUSCAR;
-------------------------------------------------------------------------------------- 



-------------------verifica se dado ja foi cadastrado--------------------------------- 
  FUNCTION  FN_VERIFICAREXISTE ( pv_conteudo    IN  varchar2,
                                 pn_tipoBusca   IN  number
                               ) return boolean is
  
  /*
  pn_tipoBusca
  1: busca pelo nome
  */
  
  vb_existe  boolean;
  
  vn_qtde    number;
  
  cursor Busca_bairroNome is
  select count(*)
  from   bairro
  where  upper(nome) = upper(pv_conteudo);
  
  
  BEGIN
    if     pn_tipoBusca = 1 then
           open  Busca_bairroNome;
           fetch Busca_bairroNome into vn_qtde;
           if    Busca_bairroNome%NotFound then
                 vn_qtde := 0;
           end   if;
           close Busca_bairroNome;
    end    if;
         
    if     vn_qtde = 0 then
           vb_existe := false;
    else   vb_existe := true;
    end    if;
    
    return vb_existe;
  END FN_VERIFICAREXISTE;
-------------------------------------------------------------------------------------- 
END PKG_BAIRRO;
```

## PKG_CIDADAO

### Specification
```
create or replace PACKAGE PKG_CIDADAO AS 
vr_cidadao   cidadao%ROWTYPE;
PROCEDURE PR_INCLUIR        ( pr_cidadao  IN  cidadao%ROWTYPE,
                               pv_erro        OUT varchar2
                            );
PROCEDURE PR_ALTERAR         ( pr_cidadao  IN  cidadao%ROWTYPE );
PROCEDURE PR_EXCLUIR         ( pn_id          IN  number );
FUNCTION  FN_BUSCAR          ( pn_id          IN  number )   return cidadao%ROWTYPE;
FUNCTION  FN_BUSCAR          ( pv_conteudo    IN  varchar2,
                               pn_tipoBusca   IN  number
                             ) return cidadao%ROWTYPE;
FUNCTION  FN_VERIFICAREXISTE ( pv_conteudo    IN  varchar2,
                               pn_tipoBusca   IN  number
                             ) return boolean;

END PKG_CIDADAO;

```

### Body
```
create or replace PACKAGE BODY PKG_CIDADAO AS

--------------------------procedure para incluir dados ---------------------------------------
PROCEDURE PR_INCLUIR ( pr_cidadao  IN  cidadao%ROWTYPE,
                         pv_erro        OUT varchar2
                       ) AS
  
  vb_existe   boolean;
  
  vn_id       number;
  
  BEGIN
    pv_erro   := '';

    
    --verifica se os dados informados não são nulos
    if      pr_cidadao.primeironome is null then
            pv_erro := 'Primeiro Nome não foi informado';
    elsif   pr_cidadao.nomemeio is null then
            pv_erro := 'Nome do Meio não foi informada';
    elsif   pr_cidadao.ultimonome is null then
            pv_erro := 'Ultimo Nome não foi informado';
    elsif   pr_cidadao.email is null then
            pv_erro := 'Email não foi informado';
    elsif   pr_cidadao.telefone is null then
            pv_erro := 'Telefone não foi informado';
    elsif   pr_cidadao.datanascimento is null then
            pv_erro := 'Data de Nascimento não foi informado';      
    elsif   pr_cidadao.senha is null then
            pv_erro := 'Senha não foi informada'; 
    elsif   pr_cidadao.genero is null then
            pv_erro := 'Genêro não foi informado';
    elsif   pr_cidadao.idbairro is null then
            pv_erro := 'ID referente ao Bairro não foi informado';                                        
    end  if; 
    
    vb_existe := FN_VERIFICAREXISTE ( pv_conteudo    => (pr_cidadao.primeironome || ' ' || pr_cidadao.nomemeio || ' ' || pr_cidadao.ultimonome),
                                      pn_tipoBusca   => 1
                                      );

    if   vb_existe then
         pv_erro := 'Cidadao já cadastrado para o nome informado';
    end  if;   


    --inclusao
    if   pv_erro is null then
         select CIDADAO_SEQ.nextval
         into   vn_id
         from   dual;
    
         insert into cidadao (id, primeironome, nomemeio, ultimonome, email, telefone, datanascimento, senha, genero, idbairro)
                values          (vn_id, pr_cidadao.primeironome, pr_cidadao.nomemeio, pr_cidadao.ultimonome, pr_cidadao.email, pr_cidadao.telefone, pr_cidadao.datanascimento, pr_cidadao.senha, pr_cidadao.genero, pr_cidadao.idbairro);
    end  if;

END PR_INCLUIR;

---------------------------------------------------------------------------------------



-------------------------procedure alterar dados---------------------------------------
  PROCEDURE PR_ALTERAR ( pr_cidadao  IN  cidadao%ROWTYPE ) AS
  BEGIN
    update cidadao
    set    primeironome  = pr_cidadao.primeironome, 
            nomemeio  = pr_cidadao.nomemeio, 
            ultimonome  = pr_cidadao.ultimonome, 
            email  = pr_cidadao.email, 
            telefone  = pr_cidadao.telefone, 
            datanascimento  = pr_cidadao.datanascimento, 
            senha  = pr_cidadao.senha, 
            genero  = pr_cidadao.genero, 
            idbairro  = pr_cidadao.idbairro
    where  id    = pr_cidadao.id;
  END PR_ALTERAR;
---------------------------------------------------------------------------------------




-------------------------procedure excluir dados---------------------------------------

  PROCEDURE PR_EXCLUIR ( pn_id          IN  number ) AS
  BEGIN
    delete from cidadao
    where  id = pn_id;
  END PR_EXCLUIR;
---------------------------------------------------------------------------------------




---------------------------função buscar pelo id---------------------------------------
  FUNCTION  FN_BUSCAR  ( pn_id          IN  number ) return cidadao%ROWTYPE is
  
  vr_cidadao   cidadao%ROWTYPE;
  
  cursor Busca_cidadao is
  select id, primeironome, nomemeio, ultimonome, email, telefone, datanascimento, senha, genero, idbairro
  from   cidadao
  where  id = pn_id;
  
  BEGIN
    open   Busca_cidadao;
    fetch  Busca_cidadao into vr_cidadao.id, vr_cidadao.primeironome, vr_cidadao.nomemeio, vr_cidadao.ultimonome, vr_cidadao.email, vr_cidadao.telefone, vr_cidadao.datanascimento, vr_cidadao.senha, vr_cidadao.genero, vr_cidadao.idbairro;
    if     Busca_cidadao%NotFound then
           vr_cidadao := null;
    end    if;
    close  Busca_cidadao;
    
    RETURN vr_cidadao;
  END FN_BUSCAR;
-------------------------------------------------------------------------------------- 


-----------------------------função para buscar dado----------------------------------
  FUNCTION  FN_BUSCAR  ( pv_conteudo    IN  varchar2,
                         pn_tipoBusca   IN  number
                       ) return cidadao%ROWTYPE is
   
  /*
  pn_tipoBusca
  1: busca pelo dataentrega
  2: busca pela quantidade
  */
  
  vr_cidadao   cidadao%ROWTYPE;
  
  cursor Busca_cidadaoNome is
  select id, primeironome, nomemeio, ultimonome, email, telefone, datanascimento, senha, genero, idbairro
  from   cidadao
  where  upper(primeironome || ' ' || nomemeio || ' ' || ultimonome) = upper(pv_conteudo);
  
  BEGIN
    if     pn_tipoBusca = 1 then
           open   Busca_cidadaoNome;
           fetch  Busca_cidadaoNome into vr_cidadao.id, vr_cidadao.primeironome, vr_cidadao.nomemeio, vr_cidadao.ultimonome, vr_cidadao.email, vr_cidadao.telefone, vr_cidadao.datanascimento, vr_cidadao.senha, vr_cidadao.genero, vr_cidadao.idbairro;
           if     Busca_cidadaoNome%NotFound then
                  vr_cidadao := null;
           end    if;
           close  Busca_cidadaoNome;
    end    if;
    
   
    
    RETURN vr_cidadao;
  END FN_BUSCAR;
-------------------------------------------------------------------------------------- 

-------------------verifica se dado ja foi cadastrado--------------------------------- 
  FUNCTION  FN_VERIFICAREXISTE ( pv_conteudo    IN  varchar2,
                                 pn_tipoBusca   IN  number
                               ) return boolean is
  
  /*
  pn_tipoBusca
  1: busca pelo nome
  2: busca pela sigla
  */
  
  vb_existe  boolean;
  
  vn_qtde    number;
  

  cursor Busca_cidadaoNome is
  select count(*)
  from   cidadao
  where  upper(primeironome || ' ' || nomemeio || ' ' || ultimonome) = upper(pv_conteudo);
  

  
  BEGIN
    if     pn_tipoBusca = 1 then
           open  Busca_cidadaoNome;
           fetch Busca_cidadaoNome into vn_qtde;
           if    Busca_cidadaoNome%NotFound then
                 vn_qtde := 0;
           end   if;
           close Busca_cidadaoNome;
    end    if;
         
    if     vn_qtde = 0 then
           vb_existe := false;
    else   vb_existe := true;
    end    if;
    
    return vb_existe;
  END FN_VERIFICAREXISTE;
-------------------------------------------------------------------------------------- 


END PKG_CIDADAO;
```

## PKG_PONTOCOLETA

### Specification
```
create or replace PACKAGE PKG_PONTOCOLETA AS 
vr_pontocoleta   pontocoleta%ROWTYPE;
PROCEDURE PR_INCLUIR        ( pr_pontocoleta  IN  pontocoleta%ROWTYPE,
                               pv_erro        OUT varchar2
                            );
PROCEDURE PR_ALTERAR         ( pr_pontocoleta  IN  pontocoleta%ROWTYPE );
PROCEDURE PR_EXCLUIR         ( pn_id          IN  number );
FUNCTION  FN_BUSCAR          ( pn_id          IN  number )   return pontocoleta%ROWTYPE;
FUNCTION  FN_BUSCAR          ( pv_conteudo    IN  varchar2,
                               pn_tipoBusca   IN  number
                             ) return pontocoleta%ROWTYPE;
FUNCTION  FN_VERIFICAREXISTE ( pv_conteudo    IN  varchar2,
                               pn_tipoBusca   IN  number
                             ) return boolean;
END PKG_PONTOCOLETA;
```

### Body
```
create or replace PACKAGE BODY PKG_PONTOCOLETA AS

--------------------------procedure para incluir dados ---------------------------------------
PROCEDURE PR_INCLUIR ( pr_pontocoleta  IN  pontocoleta%ROWTYPE,
                         pv_erro        OUT varchar2
                       ) AS
  
  vb_existe   boolean;
  
  vn_id       number;
  
  BEGIN
    pv_erro   := '';

    
    --verifica se os dados informados não são nulos
    if      pr_pontocoleta.nome is null then
            pv_erro := 'Nome não foi informado';
    end  if; 
    

    vb_existe := FN_VERIFICAREXISTE ( pv_conteudo    => pr_pontocoleta.nome,
                                      pn_tipoBusca   => 1
                                      );

    if   vb_existe then
         pv_erro := 'Ponto de Coleta já cadastrado para o nome informado';
    end  if;
    
    --inclusao
    if   pv_erro is null then
         select PONTOCOLETA_SEQ.nextval
         into   vn_id
         from   dual;
    
         insert into pontocoleta (id,    nome,               idbairro)
                values          (vn_id, pr_pontocoleta.nome, pr_pontocoleta.idbairro);
    end  if;

END PR_INCLUIR;

---------------------------------------------------------------------------------------



-------------------------procedure alterar dados---------------------------------------
  PROCEDURE PR_ALTERAR ( pr_pontocoleta  IN  pontocoleta%ROWTYPE ) AS
  BEGIN
    update pontocoleta
    set    nome  = pr_pontocoleta.nome,
           idbairro = pr_pontocoleta.idbairro
    where  id    = pr_pontocoleta.id;
  END PR_ALTERAR;
---------------------------------------------------------------------------------------




-------------------------procedure excluir dados---------------------------------------

  PROCEDURE PR_EXCLUIR ( pn_id          IN  number ) AS
  BEGIN
    delete from pontocoleta
    where  id = pn_id;
  END PR_EXCLUIR;
---------------------------------------------------------------------------------------




---------------------------função buscar pelo id---------------------------------------
  FUNCTION  FN_BUSCAR  ( pn_id          IN  number ) return pontocoleta%ROWTYPE is
  
  vr_pontocoleta   pontocoleta%ROWTYPE;
  
  cursor Busca_pontocoleta is
  select id, nome, idbairro
  from   pontocoleta
  where  id = pn_id;
  
  BEGIN
    open   Busca_pontocoleta;
    fetch  Busca_pontocoleta into vr_pontocoleta.id, vr_pontocoleta.nome, vr_pontocoleta.idbairro;
    if     Busca_pontocoleta%NotFound then
           vr_pontocoleta := null;
    end    if;
    close  Busca_pontocoleta;
    
    RETURN vr_pontocoleta;
  END FN_BUSCAR;
-------------------------------------------------------------------------------------- 


-----------------------------função para buscar dado----------------------------------
  FUNCTION  FN_BUSCAR  ( pv_conteudo    IN  varchar2,
                         pn_tipoBusca   IN  number
                       ) return pontocoleta%ROWTYPE is
   
  /*
  pn_tipoBusca
  1: busca pelo nome
  */
  
  vr_pontocoleta   pontocoleta%ROWTYPE;
  
  cursor Busca_pontocoletaNome is
  select id, nome, idbairro
  from   pontocoleta
  where  upper(nome) = upper(pv_conteudo);

  BEGIN
    if     pn_tipoBusca = 1 then
           open   Busca_pontocoletaNome;
           fetch  Busca_pontocoletaNome into vr_pontocoleta.id, vr_pontocoleta.nome, vr_pontocoleta.idbairro;
           if     Busca_pontocoletaNome%NotFound then
                  vr_pontocoleta := null;
           end    if;
           close  Busca_pontocoletaNome;
    end    if;
    
    
    RETURN vr_pontocoleta;
  END FN_BUSCAR;
-------------------------------------------------------------------------------------- 



-------------------verifica se dado ja foi cadastrado--------------------------------- 
  FUNCTION  FN_VERIFICAREXISTE ( pv_conteudo    IN  varchar2,
                                 pn_tipoBusca   IN  number
                               ) return boolean is
  
  /*
  pn_tipoBusca
  1: busca pelo nome
  */
  
  vb_existe  boolean;
  
  vn_qtde    number;
  
  cursor Busca_pontocoletaNome is
  select count(*)
  from   pontocoleta
  where  upper(nome) = upper(pv_conteudo);
  
  
  BEGIN
    if     pn_tipoBusca = 1 then
           open  Busca_pontocoletaNome;
           fetch Busca_pontocoletaNome into vn_qtde;
           if    Busca_pontocoletaNome%NotFound then
                 vn_qtde := 0;
           end   if;
           close Busca_pontocoletaNome;
    end    if;
         
    if     vn_qtde = 0 then
           vb_existe := false;
    else   vb_existe := true;
    end    if;
    
    return vb_existe;
  END FN_VERIFICAREXISTE;
-------------------------------------------------------------------------------------- 
END PKG_PONTOCOLETA;
```

## PKG_RESIDUO

### Specification
```
create or replace PACKAGE PKG_RESIDUO AS 
vr_residuo   residuo%ROWTYPE;
PROCEDURE PR_INCLUIR        ( pr_residuo  IN  residuo%ROWTYPE,
                               pv_erro        OUT varchar2
                            );
PROCEDURE PR_ALTERAR         ( pr_residuo  IN  residuo%ROWTYPE );
PROCEDURE PR_EXCLUIR         ( pn_id          IN  number );
FUNCTION  FN_BUSCAR          ( pn_id          IN  number )   return residuo%ROWTYPE;
FUNCTION  FN_BUSCAR          ( pv_conteudo    IN  varchar2,
                               pn_tipoBusca   IN  number
                             ) return residuo%ROWTYPE;
FUNCTION  FN_VERIFICAREXISTE ( pv_conteudo    IN  varchar2,
                               pn_tipoBusca   IN  number
                             ) return boolean;
END PKG_RESIDUO;

```

### Body
```
create or replace PACKAGE BODY PKG_RESIDUO AS

--------------------------procedure para incluir dados ---------------------------------------
PROCEDURE PR_INCLUIR ( pr_residuo  IN  residuo%ROWTYPE,
                         pv_erro        OUT varchar2
                       ) AS
  
  vb_existe   boolean;
  
  vn_id       number;
  
  BEGIN
    pv_erro   := '';

    
    --verifica se os dados informados não são nulos
    if      pr_residuo.dataentrega is null then
            pv_erro := 'Nome não foi informado';
    elsif   pr_residuo.quantidade is null then
            pv_erro := 'Sigla não foi informada';
    elsif   pr_residuo.idcidadao is null then
            pv_erro := 'ID referente ao Cidadão não foi informado';
    elsif   pr_residuo.idpontocoleta is null then
            pv_erro := 'ID referente ao Ponto de Coleta não foi informado';
    elsif   pr_residuo.idtiporesiduo is null then
            pv_erro := 'ID referente ao Tipo de Resíduo não foi informado';
    end  if; 
    

    vb_existe := FN_VERIFICAREXISTE ( pv_conteudo    => pr_residuo.dataentrega,
                                      pn_tipoBusca   => 1
                                      );
    
    --inclusao
    if   pv_erro is null then
         select RESIDUO_SEQ.nextval
         into   vn_id
         from   dual;
    
         insert into residuo (id, dataentrega, quantidade, idcidadao, idpontocoleta, idtiporesiduo)
                values          (vn_id, pr_residuo.dataentrega, pr_residuo.quantidade, pr_residuo.idcidadao, pr_residuo.idpontocoleta, pr_residuo.idtiporesiduo);
    end  if;

END PR_INCLUIR;

---------------------------------------------------------------------------------------



-------------------------procedure alterar dados---------------------------------------
  PROCEDURE PR_ALTERAR ( pr_residuo  IN  residuo%ROWTYPE ) AS
  BEGIN
    update residuo
    set    dataentrega  = pr_residuo.dataentrega,
           quantidade = pr_residuo.quantidade,
           idcidadao = pr_residuo.idcidadao,
           idpontocoleta = pr_residuo.idpontocoleta,
           idtiporesiduo = pr_residuo.idtiporesiduo
    where  id    = pr_residuo.id;
  END PR_ALTERAR;
---------------------------------------------------------------------------------------




-------------------------procedure excluir dados---------------------------------------

  PROCEDURE PR_EXCLUIR ( pn_id          IN  number ) AS
  BEGIN
    delete from residuo
    where  id = pn_id;
  END PR_EXCLUIR;
---------------------------------------------------------------------------------------




---------------------------função buscar pelo id---------------------------------------
  FUNCTION  FN_BUSCAR  ( pn_id          IN  number ) return residuo%ROWTYPE is
  
  vr_residuo   residuo%ROWTYPE;
  
  cursor Busca_residuo is
  select id, dataentrega, quantidade, idcidadao, idpontocoleta, idtiporesiduo
  from   residuo
  where  id = pn_id;
  
  BEGIN
    open   Busca_residuo;
    fetch  Busca_residuo into vr_residuo.id, vr_residuo.dataentrega, vr_residuo.quantidade, vr_residuo.idcidadao, vr_residuo.idpontocoleta, vr_residuo.idtiporesiduo;
    if     Busca_residuo%NotFound then
           vr_residuo := null;
    end    if;
    close  Busca_residuo;
    
    RETURN vr_residuo;
  END FN_BUSCAR;
-------------------------------------------------------------------------------------- 


-----------------------------função para buscar dado----------------------------------
  FUNCTION  FN_BUSCAR  ( pv_conteudo    IN  varchar2,
                         pn_tipoBusca   IN  number
                       ) return residuo%ROWTYPE is
   
  /*
  pn_tipoBusca
  1: busca pelo dataentrega
  2: busca pela quantidade
  */
  
  vr_residuo   residuo%ROWTYPE;
  
  cursor Busca_residuoDataEntrega is
  select id, dataentrega, quantidade, idcidadao, idpontocoleta, idtiporesiduo
  from   residuo
  where  upper(dataentrega) = upper(pv_conteudo);
  
  cursor Busca_residuoQuantidade is
  select id, dataentrega, quantidade, idcidadao, idpontocoleta, idtiporesiduo
  from   residuo
  where  upper(quantidade) = upper(pv_conteudo);
  
  BEGIN
    if     pn_tipoBusca = 1 then
           open   Busca_residuoDataEntrega;
           fetch  Busca_residuoDataEntrega into vr_residuo.id, vr_residuo.dataentrega, vr_residuo.quantidade, vr_residuo.idcidadao, vr_residuo.idpontocoleta, vr_residuo.idtiporesiduo;
           if     Busca_residuoDataEntrega%NotFound then
                  vr_residuo := null;
           end    if;
           close  Busca_residuoDataEntrega;
    end    if;
    
    if     pn_tipoBusca = 2 then
           open   Busca_residuoQuantidade ;
           fetch  Busca_residuoQuantidade  into vr_residuo.id, vr_residuo.dataentrega, vr_residuo.quantidade, vr_residuo.idcidadao, vr_residuo.idpontocoleta, vr_residuo.idtiporesiduo;
           if     Busca_residuoQuantidade %NotFound then
                  vr_residuo := null;
           end    if;
           close  Busca_residuoQuantidade; 
    end    if;
    
    RETURN vr_residuo;
  END FN_BUSCAR;
-------------------------------------------------------------------------------------- 



-------------------verifica se dado ja foi cadastrado--------------------------------- 
  FUNCTION  FN_VERIFICAREXISTE ( pv_conteudo    IN  varchar2,
                                 pn_tipoBusca   IN  number
                               ) return boolean is
  
  /*
  pn_tipoBusca
  1: busca pelo dataentrega
  2: busca pela quantidade
  */
  
  vb_existe  boolean;
  
  vn_qtde    number;
  
  cursor Busca_residuoDataEntrega is
  select count(*)
  from   residuo
  where  upper(dataentrega) = upper(pv_conteudo);
  
  cursor Busca_residuoQuantidade is
  select count(*)
  from   residuo
  where  upper(quantidade) = upper(pv_conteudo);
  
  BEGIN
    if     pn_tipoBusca = 1 then
           open  Busca_residuoDataEntrega;
           fetch Busca_residuoDataEntrega into vn_qtde;
           if    Busca_residuoDataEntrega%NotFound then
                 vn_qtde := 0;
           end   if;
           close Busca_residuoDataEntrega;
    else   open  Busca_residuoQuantidade;
           fetch Busca_residuoQuantidade into vn_qtde;
           if    Busca_residuoQuantidade%NotFound then
                 vn_qtde := 0;
           end   if;
           close Busca_residuoQuantidade;
    end    if;
         
    if     vn_qtde = 0 then
           vb_existe := false;
    else   vb_existe := true;
    end    if;
    
    return vb_existe;
  END FN_VERIFICAREXISTE;
-------------------------------------------------------------------------------------- 


END PKG_RESIDUO;
```

## PKG_TIPORESIDUO

### Specification
```
create or replace PACKAGE PKG_TIPORESIDUO AS 
vr_tipoResiduo   tipoResiduo%ROWTYPE;
PROCEDURE PR_INCLUIR        ( pr_tipoResiduo  IN  tipoResiduo%ROWTYPE,
                               pv_erro        OUT varchar2
                            );
PROCEDURE PR_ALTERAR         ( pr_tipoResiduo  IN  tipoResiduo%ROWTYPE );
PROCEDURE PR_EXCLUIR         ( pn_id          IN  number );
FUNCTION  FN_BUSCAR          ( pn_id          IN  number )   return tipoResiduo%ROWTYPE;
FUNCTION  FN_BUSCAR          ( pv_conteudo    IN  varchar2,
                               pn_tipoBusca   IN  number
                             ) return tipoResiduo%ROWTYPE;
FUNCTION  FN_VERIFICAREXISTE ( pv_conteudo    IN  varchar2,
                               pn_tipoBusca   IN  number
                             ) return boolean;
END PKG_TIPORESIDUO;
```

### Body
```
create or replace PACKAGE BODY PKG_TIPORESIDUO AS

--------------------------procedure para incluir dados ---------------------------------------
PROCEDURE PR_INCLUIR ( pr_tipoResiduo  IN  tipoResiduo%ROWTYPE,
                         pv_erro        OUT varchar2
                       ) AS
  
  vb_existe   boolean;
  
  vn_id       number;
  
  BEGIN
    pv_erro   := '';

    
    --verifica se os dados informados não são nulos
    if      pr_tipoResiduo.nome is null then
            pv_erro := 'Nome não foi informado';
    end  if; 
    

    vb_existe := FN_VERIFICAREXISTE ( pv_conteudo    => pr_tipoResiduo.nome,
                                      pn_tipoBusca   => 1
                                      );

    if   vb_existe then
         pv_erro := 'Tipo de resíduo já cadastrado para o nome informado';
    end  if;
    
    --inclusao
    if   pv_erro is null then
         select TIPORESIDUO_SEQ.nextval
         into   vn_id
         from   dual;
    
         insert into tipoResiduo (id,    nome,               idunidademedida)
                values          (vn_id, pr_tipoResiduo.nome, pr_tipoResiduo.idunidademedida);
    end  if;

END PR_INCLUIR;

---------------------------------------------------------------------------------------



-------------------------procedure alterar dados---------------------------------------
  PROCEDURE PR_ALTERAR ( pr_tipoResiduo  IN  tipoResiduo%ROWTYPE ) AS
  BEGIN
    update tipoResiduo
    set    nome  = pr_tipoResiduo.nome,
           idunidademedida = pr_tipoResiduo.idunidademedida
    where  id    = pr_tipoResiduo.id;
  END PR_ALTERAR;
---------------------------------------------------------------------------------------




-------------------------procedure excluir dados---------------------------------------

  PROCEDURE PR_EXCLUIR ( pn_id          IN  number ) AS
  BEGIN
    delete from tipoResiduo
    where  id = pn_id;
  END PR_EXCLUIR;
---------------------------------------------------------------------------------------




---------------------------função buscar pelo id---------------------------------------
  FUNCTION  FN_BUSCAR  ( pn_id          IN  number ) return tipoResiduo%ROWTYPE is
  
  vr_tipoResiduo   tipoResiduo%ROWTYPE;
  
  cursor Busca_tipoResiduo is
  select id, nome, idunidademedida
  from   tipoResiduo
  where  id = pn_id;
  
  BEGIN
    open   Busca_tipoResiduo;
    fetch  Busca_tipoResiduo into vr_tipoResiduo.id, vr_tipoResiduo.nome, vr_tipoResiduo.idunidademedida;
    if     Busca_tipoResiduo%NotFound then
           vr_tipoResiduo := null;
    end    if;
    close  Busca_tipoResiduo;
    
    RETURN vr_tipoResiduo;
  END FN_BUSCAR;
-------------------------------------------------------------------------------------- 


-----------------------------função para buscar dado----------------------------------
  FUNCTION  FN_BUSCAR  ( pv_conteudo    IN  varchar2,
                         pn_tipoBusca   IN  number
                       ) return tipoResiduo%ROWTYPE is
   
  /*
  pn_tipoBusca
  1: busca pelo nome
  */
  
  vr_tipoResiduo   tipoResiduo%ROWTYPE;
  
  cursor Busca_tipoResiduoNome is
  select id, nome, idunidademedida
  from   tipoResiduo
  where  upper(nome) = upper(pv_conteudo);

  BEGIN
    if     pn_tipoBusca = 1 then
           open   Busca_tipoResiduoNome;
           fetch  Busca_tipoResiduoNome into vr_tipoResiduo.id, vr_tipoResiduo.nome, vr_tipoResiduo.idunidademedida;
           if     Busca_tipoResiduoNome%NotFound then
                  vr_tipoResiduo := null;
           end    if;
           close  Busca_tipoResiduoNome;
    end    if;
    
    
    RETURN vr_tipoResiduo;
  END FN_BUSCAR;
-------------------------------------------------------------------------------------- 



-------------------verifica se dado ja foi cadastrado--------------------------------- 
  FUNCTION  FN_VERIFICAREXISTE ( pv_conteudo    IN  varchar2,
                                 pn_tipoBusca   IN  number
                               ) return boolean is
  
  /*
  pn_tipoBusca
  1: busca pelo nome
  */
  
  vb_existe  boolean;
  
  vn_qtde    number;
  
  cursor Busca_tipoResiduoNome is
  select count(*)
  from   tipoResiduo
  where  upper(nome) = upper(pv_conteudo);
  
  
  BEGIN
    if     pn_tipoBusca = 1 then
           open  Busca_tipoResiduoNome;
           fetch Busca_tipoResiduoNome into vn_qtde;
           if    Busca_tipoResiduoNome%NotFound then
                 vn_qtde := 0;
           end   if;
           close Busca_tipoResiduoNome;
    end    if;
         
    if     vn_qtde = 0 then
           vb_existe := false;
    else   vb_existe := true;
    end    if;
    
    return vb_existe;
  END FN_VERIFICAREXISTE;
-------------------------------------------------------------------------------------- 
END PKG_TIPORESIDUO;
```

## PKG_UNIDADEMEDIDA

### Specification
```
create or replace PACKAGE PKG_UNIDADEMEDIDA AS 
vr_unidadeMedida   unidadeMedida%ROWTYPE;
PROCEDURE PR_INCLUIR        ( pr_unidadeMedida  IN  unidadeMedida%ROWTYPE,
                               pv_erro        OUT varchar2
                            );
PROCEDURE PR_ALTERAR         ( pr_unidadeMedida  IN  unidadeMedida%ROWTYPE );
PROCEDURE PR_EXCLUIR         ( pn_id          IN  number );
FUNCTION  FN_BUSCAR          ( pn_id          IN  number )   return unidadeMedida%ROWTYPE;
FUNCTION  FN_BUSCAR          ( pv_conteudo    IN  varchar2,
                               pn_tipoBusca   IN  number
                             ) return unidadeMedida%ROWTYPE;
FUNCTION  FN_VERIFICAREXISTE ( pv_conteudo    IN  varchar2,
                               pn_tipoBusca   IN  number
                             ) return boolean;
END PKG_UNIDADEMEDIDA;
```

### Body
```
create or replace PACKAGE BODY PKG_UNIDADEMEDIDA AS

--------------------------procedure para incluir dados ---------------------------------------
PROCEDURE PR_INCLUIR ( pr_unidadeMedida  IN  unidadeMedida%ROWTYPE,
                         pv_erro        OUT varchar2
                       ) AS
  
  vb_existe   boolean;
  
  vn_id       number;
  
  BEGIN
    pv_erro   := '';

    
    --verifica se os dados informados não são nulos
    if      pr_unidadeMedida.nome is null then
            pv_erro := 'Nome não foi informado';
    else if pr_unidadeMedida.sigla is null then
            pv_erro := 'Sigla não foi informada';
        end if;
    end  if; 
    

    vb_existe := FN_VERIFICAREXISTE ( pv_conteudo    => pr_unidadeMedida.nome,
                                      pn_tipoBusca   => 1
                                      );

    if   vb_existe then
         pv_erro := 'Unidade de medida já cadastrado para o nome informado';
    else vb_existe := FN_VERIFICAREXISTE ( pv_conteudo    => pr_unidadeMedida.sigla,
                                           pn_tipoBusca   => 2
                                          );
         if   vb_existe then
              pv_erro := 'Unidade de medida já cadastrado para a sigla informada';
         end  if;
    end  if;
    
    --inclusao
    if   pv_erro is null then
         select UNIDADEMEDIDA_SEQ.nextval
         into   vn_id
         from   dual;
    
         insert into unidadeMedida (id,    nome,               sigla)
                values          (vn_id, pr_unidadeMedida.nome, pr_unidadeMedida.sigla);
    end  if;

END PR_INCLUIR;

---------------------------------------------------------------------------------------



-------------------------procedure alterar dados---------------------------------------
  PROCEDURE PR_ALTERAR ( pr_unidadeMedida  IN  unidadeMedida%ROWTYPE ) AS
  BEGIN
    update unidadeMedida
    set    nome  = pr_unidadeMedida.nome,
           sigla = pr_unidadeMedida.sigla
    where  id    = pr_unidadeMedida.id;
  END PR_ALTERAR;
---------------------------------------------------------------------------------------




-------------------------procedure excluir dados---------------------------------------

  PROCEDURE PR_EXCLUIR ( pn_id          IN  number ) AS
  BEGIN
    delete from unidadeMedida
    where  id = pn_id;
  END PR_EXCLUIR;
---------------------------------------------------------------------------------------




---------------------------função buscar pelo id---------------------------------------
  FUNCTION  FN_BUSCAR  ( pn_id          IN  number ) return unidadeMedida%ROWTYPE is
  
  vr_unidadeMedida   unidadeMedida%ROWTYPE;
  
  cursor Busca_unidadeMedida is
  select id, nome, sigla
  from   unidadeMedida
  where  id = pn_id;
  
  BEGIN
    open   Busca_unidadeMedida;
    fetch  Busca_unidadeMedida into vr_unidadeMedida.id, vr_unidadeMedida.nome, vr_unidadeMedida.sigla;
    if     Busca_unidadeMedida%NotFound then
           vr_unidadeMedida := null;
    end    if;
    close  Busca_unidadeMedida;
    
    RETURN vr_unidadeMedida;
  END FN_BUSCAR;
-------------------------------------------------------------------------------------- 


-----------------------------função para buscar dado----------------------------------
  FUNCTION  FN_BUSCAR  ( pv_conteudo    IN  varchar2,
                         pn_tipoBusca   IN  number
                       ) return unidadeMedida%ROWTYPE is
   
  /*
  pn_tipoBusca
  1: busca pelo nome
  2: busca pela sigla
  */
  
  vr_unidadeMedida   unidadeMedida%ROWTYPE;
  
  cursor Busca_unidadeMedidaNome is
  select id, nome, sigla
  from   unidadeMedida
  where  upper(nome) = upper(pv_conteudo);
  
  cursor Busca_unidadeMedidaSigla is
  select id, nome, sigla
  from   unidadeMedida
  where  upper(sigla) = upper(pv_conteudo);
  
  BEGIN
    if     pn_tipoBusca = 1 then
           open   Busca_unidadeMedidaNome;
           fetch  Busca_unidadeMedidaNome into vr_unidadeMedida.id, vr_unidadeMedida.nome, vr_unidadeMedida.sigla;
           if     Busca_unidadeMedidaNome%NotFound then
                  vr_unidadeMedida := null;
           end    if;
           close  Busca_unidadeMedidaNome;
    end    if;
    
    if     pn_tipoBusca = 2 then
           open   Busca_unidadeMedidaSigla;
           fetch  Busca_unidadeMedidaSigla into vr_unidadeMedida.id, vr_unidadeMedida.nome, vr_unidadeMedida.sigla;
           if     Busca_unidadeMedidaSigla%NotFound then
                  vr_unidadeMedida := null;
           end    if;
           close  Busca_unidadeMedidaSigla;
    end    if;
    
    RETURN vr_unidadeMedida;
  END FN_BUSCAR;
-------------------------------------------------------------------------------------- 



-------------------verifica se dado ja foi cadastrado--------------------------------- 
  FUNCTION  FN_VERIFICAREXISTE ( pv_conteudo    IN  varchar2,
                                 pn_tipoBusca   IN  number
                               ) return boolean is
  
  /*
  pn_tipoBusca
  1: busca pelo nome
  2: busca pela sigla
  */
  
  vb_existe  boolean;
  
  vn_qtde    number;
  
  cursor Busca_unidadeMedidaNome is
  select count(*)
  from   unidadeMedida
  where  upper(nome) = upper(pv_conteudo);
  
  cursor Busca_unidadeMedidaSigla is
  select count(*)
  from   unidadeMedida
  where  upper(sigla) = upper(pv_conteudo);
  
  BEGIN
    if     pn_tipoBusca = 1 then
           open  Busca_unidadeMedidaNome;
           fetch Busca_unidadeMedidaNome into vn_qtde;
           if    Busca_unidadeMedidaNome%NotFound then
                 vn_qtde := 0;
           end   if;
           close Busca_unidadeMedidaNome;
    else   open  Busca_unidadeMedidaSigla;
           fetch Busca_unidadeMedidaSigla into vn_qtde;
           if    Busca_unidadeMedidaSigla%NotFound then
                 vn_qtde := 0;
           end   if;
           close Busca_unidadeMedidaSigla;
    end    if;
         
    if     vn_qtde = 0 then
           vb_existe := false;
    else   vb_existe := true;
    end    if;
    
    return vb_existe;
  END FN_VERIFICAREXISTE;
-------------------------------------------------------------------------------------- 


END PKG_UNIDADEMEDIDA;
```
