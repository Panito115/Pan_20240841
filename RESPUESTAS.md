# Respuestas

## P2. Fallas del Jenkinsfile

1. El `bru run` del stage `Regresion` terminaba en `|| true`, que forzaba código de salida 0. La regresión nunca podía reprobar el build, así que el pipeline no servía de puerta: pasaran o no las pruebas, el build salía verde.
2. El `junit` del bloque `post > always` leía `bruno/smoke/results.xml`, un archivo que ninguna etapa genera. Jenkins no publicaba los resultados de la regresión y el Test Result quedaba vacío, con `allowEmptyResults: true` tapando el error.
3. El stage `Build image` corría antes que `Regresion`. La imagen se construía sin haber pasado las pruebas, de modo que una regresión rota igual producía un artefacto publicable.

## P3. Builds

| Paso | Build | SHA | Resultado |
|---|---|---|---|
| 1. Jenkinsfile corregido | #2 | 8dcf9d1 | SUCCESS |
| 2. Prueba del tope de Q60 | #3 | a938dac | FAILURE |
| 3. Fix del tope en api.py | #4 | 85a34a5 | SUCCESS |

## Aserción que falló en el build rojo

```
14-cobro-con-tope.res.body.monto eq 60
```

Detalle: `expected 70 to equal 60`. El ticket de 361 minutos se cobraba a Q70 (7 horas x Q10) porque no se aplicaba el tope de Q60.
