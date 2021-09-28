---
layout: page
title:  Função Diferença entre Datas
subtitle:  BDAE62 - Atividade
---

---



## Função
```
create or replace function "FN_CALCULARDIFDATAS"
(pd_data1 in DATE,
pd_data2 in DATE,
pv_opcao in VARCHAR2)
return NUMBER
is

DIF NUMBER;

begin
    DIF := TRUNC(PD_DATA1 - PD_DATA2);

    IF PV_OPCAO = 'D' THEN
        return DIF;
    END IF;

    IF PV_OPCAO = 'M' THEN
        DIF := TRUNC(DIF / 30.4);
        RETURN DIF;
    END IF;

    IF PV_OPCAO = 'A' THEN 
        DIF := TRUNC(DIF / 365.25);
        RETURN DIF;
    END IF;

    
end;

```

## Teste

```
SELECT 
FN_CALCULARDIFDATAS('09/28/2021', '01/26/2001', 'D') AS DIF_DIAS, 
FN_CALCULARDIFDATAS('09/28/2021', '01/26/2001', 'M') AS DIF_MESES,  
FN_CALCULARDIFDATAS('09/28/2021', '01/26/2001', 'A') AS DIF_ANOS
FROM DUAL  
```

### Saída

```
"DIF_DIAS","DIF_MESES","DIF_ANOS"
"7550","248","20"
```
