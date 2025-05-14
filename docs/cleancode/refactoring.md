# Refactorización

## Introducción

A continuación se presentan diferentes técnicas para refactorizar código. En cada apartado encontrarás el código original y el refactorizado. Es posible y recomendable aplicar varias de las técnicas a un mismo código.

## Renombrar variables y métodos

Usar nombres adecuados para variables y métodos.

```java
public class Conversor {
	public float conv (float c) {
		float x = c * 166.386f;
		return x;
	   }
}
```

Acciones realizadas:
- Se define como constante `EUROS_PESETAS_CHANGE_RATE`.
- El método `conv` se renombra a `eurosToPesetas`.
- La variable `c` se renombra a `euros`.
- La variable `x` se renombra a `pesetas`.

```java
public class Conversor {
	private static final float EUROS_PESETAS_CHANGE_RATE = 166.386f;

	public float eurosToPesetas (float euros) {
		float pesetas = euros * EUROS_PESETAS_CHANGE_RATE;
		return pesetas;
	   }
}
```

