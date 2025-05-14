# Refactorización

## Introducción

A continuación se presentan diferentes técnicas para refactorizar código. En cada apartado encontrarás el código original y el refactorizado. Es posible y recomendable aplicar varias de las técnicas a un mismo código.

## Renombrar variables y métodos

Usar nombres adecuados para variables y métodos.

```java
public class Conversor {
	public float conv (float c) {
		float x = c * 92678.27f;
		return x;
	   }
}
```

??? info Acciones realizadas

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

??? info Acciones realizadas

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

??? info Acciones realizadas

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