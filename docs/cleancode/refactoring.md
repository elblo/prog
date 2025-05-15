# Refactorización

## Introducción

> "Cualquiera puede escribir código que un ordenador pueda entender. Los buenos programadores son aquellos que escriben código que los humanos puedan entender." - Martin Fowler

A continuación se presentan diferentes técnicas para refactorizar código. En cada apartado encontrarás el código original y el refactorizado. Es posible y recomendable aplicar varias de las técnicas a un mismo código.

> Refactorizar es **cambiar el código mejorando su estructura** sin alterar su comportamiento.

## Renombrar variables y métodos

Cambiar nombres de variables y métodos por otros adecuados para su contexto.

En el siguiente ejemplo no queda claro qué hace el método o qué almacenan las variables.

``` java title="rename/Conversor.java"
public class Conversor {
	public float conv (float c) {
		float x = c * 92678.27f;
		return x;
	   }
}
```

??? info "Refactorizacón"

    - Se define como constante `BTC_EUR_CHANGE_RATE`.
    - El método `conv` se renombra a `bitcoinsToEuros`.
    - La variable `c` se renombra a `bitcoins`.
    - La variable `x` se renombra a `euros`.

    ``` java title="rename/refactores/Conversor.java"
    public class Conversor {
        private static final float BTC_EUR_CHANGE_RATE = 92678.27f;

        public float bitcoinsToEuros (float bitcoins) {
            float euros = bitcoins * EUR_BTC_CHANGE_RATE;
            return euros;
        }
    }
    ```

## Ocultar propiedades y métodos

Usar nombres adecuados para variables y métodos.

```java
public class Conversor {
	public float conv (float c) {
		float x = c * 92678.27f;
		return x;
	   }
}
```

??? info "Refactorizacón"

    - Se define como constante `BTC_EUR_CHANGE_RATE`.
    - El método `conv` se renombra a `bitcoinsToEuros`.
    - La variable `c` se renombra a `bitcoins`.
    - La variable `x` se renombra a `euros`.

    ```java
    public class Conversor {
        private static final float BTC_EUR_CHANGE_RATE = 92678.27f;

        public float bitcoinsToEuros (float bitcoins) {
            float euros = bitcoins * EUR_BTC_CHANGE_RATE;
            return euros;
        }
    }
    ```

## Magic numbers

Usar nombres adecuados para variables y métodos.

```java
public class Conversor {
	public float conv (float c) {
		float x = c * 92678.27f;
		return x;
	   }
}
```

??? info "Refactorizacón"

    - Se define como constante `BTC_EUR_CHANGE_RATE`.
    - El método `conv` se renombra a `bitcoinsToEuros`.
    - La variable `c` se renombra a `bitcoins`.
    - La variable `x` se renombra a `euros`.

    ```java
    public class Conversor {
        private static final float BTC_EUR_CHANGE_RATE = 92678.27f;

        public float bitcoinsToEuros (float bitcoins) {
            float euros = bitcoins * EUR_BTC_CHANGE_RATE;
            return euros;
        }
    }
    ```