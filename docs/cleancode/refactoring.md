# Refactorización

## Introducción

Refactorizar es **cambiar el código mejorando su estructura** sin alterar su comportamiento.

> "Cualquiera puede escribir código que un ordenador pueda entender. Los buenos programadores son aquellos que escriben código que los humanos puedan entender." - Martin Fowler

A continuación se presentan diferentes técnicas para refactorizar código. En cada apartado encontrarás el código original y el refactorizado. Es posible y recomendable aplicar varias de las técnicas a un mismo código.


## Renombrar variables y métodos

Cambiar nombres de variables y métodos por otros adecuados para su contexto aporta una mayor legibilidad del código y aclara su propósito.

En el siguiente ejemplo no queda claro qué hace el método o qué almacenan las variables.

``` java title="rename/Conversor.java"
public class Conversor {
	public float conv (float c) {
		float x = c * 92678.27f;
		return x;
	   }
}
```

Todos los IDEs modernos tienen herramientas de refactorización que es conveniente saber utilizar para evitar despistes al realizar cambios manuales. En IntelliJ IDEA, seleccionar el método/variable y `Refactor > Rename`.

??? abstract "Refactorizacón"

    - Se define como constante `BTC_EUR_CHANGE_RATE`.
    - El método `conv` se renombra a `bitcoinsToEuros`.
    - La variable `c` se renombra a `bitcoins`.
    - La variable `x` se renombra a `euros`.

    ``` java title="rename/refactored/Conversor.java"
    public class Conversor {
        private static final float BTC_EUR_CHANGE_RATE = 92678.27f;

        public float bitcoinsToEuros (float bitcoins) {
            float euros = bitcoins * EUR_BTC_CHANGE_RATE;
            return euros;
        }
    }
    ```

## Encapsulación

Ocultar propiedades y métodos que no se usen desde fuera haciéndolos privados mejora las operaciones de acceso sin exponer detalles internos de la propia clase.

``` java title=":material-file-code: encapsulate/Customer.java"
public class Customer {
	String name;
	int id;

	public Customer() {
		init();
	}

	public void init() {
		name = "Eugene Krabs";
		id = 42;
	}

	public String toString() {
		return id + ":" + name;
	}
}
```

En IntelliJ IDEA, seleccionar la variable y `Refactor > Encapsulate Fields`.

??? abstract "Refactorizacón"

    - Se definen como privados los atributos `name` e `id`.    
    - Se añaden sus getters y setters correspondientes.
    - Se define como privado el método `init` porque sólo se usa internamente.

    ``` java title="rename/refactored/Conversor.java"
    public class Customer {
        private String name;
        private int id;

        public Customer() {
            init();
        }

        private void init() {
            setName("Eugene Krabs");
            setId(42);
        }

        String getName(){ return name; }
        void setName(String name){ this.name = name; }

        int getId(){ return id; }
        void setId(int id){ this.id = id; }

        public String toString() {
            return getId() + ":" + getName();
        }
    }
    ```


## Magic numbers

