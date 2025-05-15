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

``` java title="encapsulate/Customer.java"
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

## Extraer constantes

Extraer valores literales a constantes para aclarar el propósito del valor, aumentar la legibilidad, facilitar su cambio o incluso facilitar el uso de valores complejos como PI.

``` java title="magicnumbers/PasswordGenerator.java" hl_lines="6"
public class PasswordGenerator {
	private Random random = new Random();
	private String characters = "abcdefghijkmnopqrstuvwxyz23456789";

	public String generatePassword(int length) throws Exception {
		if (length < 6 || length > 15) {
			throw new Exception("Wrong password length: " + length);
		} else {
			String password = "";

			for (int i = 0; i < length; i++)
				password += characters.charAt(random.nextInt(characters.length()));

			return password;
		}
	}
}
```

En IntelliJ IDEA, seleccionar el valor literal y `Refactor > Introduce Constant`.

??? abstract "Refactorización"

    - Crear constantes `MAX_PASSWORD_LENGTH` y `MIN_PASSWORD_LENGTH` para los valores literales 15 y 6.

    ``` java title="magicnumbers/refactored/PasswordGenerator.java" hl_lines="2 3 8"
    public class PasswordGenerator { 
        private static final int MAX_PASSWORD_LENGTH = 15;
        private static final int MIN_PASSWORD_LENGTH = 6;
        private Random random = new Random();
        private String characters = "abcdefghijkmnopqrstuvwxyz23456789";

        public String generatePassword(int length) throws Exception {
            if (length < MIN_PASSWORD_LENGTH || length > MAX_PASSWORD_LENGTH) {
                throw new Exception("Wrong password length: " + length);
            } else {
                String password = "";

                for (int i = 0; i < length; i++)
                    password += characters.charAt(random.nextInt(characters.length()));

                return password;
            }
        }
    }
    ```

## Extraer métodos

Extraer el código de un método en tantos otros como sea necesario para que cada uno tenga un tamaño máximo de unas 5 líneas aumenta la legibilidad y reduce el código duplicado.

???+ warning "Código mejorable..."
    ``` java title="extractmethod/UrlNormalizer.java"
    public class UrlNormalizer {

        public String normalize(String title) {
            String url = "";
            // First we trim whitespaces
            url = title.trim();

            // Remove special chars
            String specialRemoved = "";
            for (int i = 0; i < url.length(); i++) {
                if (url.charAt(i) != ',' && url.charAt(i) != ':' 
                        && url.charAt(i) != '.' && url.charAt(i) != '?') {
                    specialRemoved += url.charAt(i);
                }
            }

            url = specialRemoved;

            // Replace white spaces with hyphens
            String spacesReplaced = "";
            for (int i = 0; i < url.length(); i++) {
                if (url.charAt(i) == ' ') {
                    spacesReplaced += "-";
                } else {
                    spacesReplaced += url.charAt(i);
                }
            }
            url = spacesReplaced;

            // lowercase everything
            url = url.toLowerCase();

            return url;
        }
    }
    ```

En IntelliJ IDEA, seleccionar el código que nos interese extraer y `Refactor > Extract Method`.

??? abstract "Refactorización"

    - acciones

    ``` java title="extractmethod/refactored/UrlNormalizer.java"
    public class UrlNormalizer {

        public String normalize(String title) {
            String url = trimSpaces(title);

            url = removeSpecialChars(url);
            url = replaceSpaces(url);
            url = url.toLowerCase();

            return url;
        }

        private String replaceSpaces(String url) {
            String spacesReplaced = "";
            for (int i = 0; i < url.length(); i++) {
                if (url.charAt(i) == ' ') {
                    spacesReplaced += "-";
                } else {
                    spacesReplaced += url.charAt(i);
                }
            }
            url = spacesReplaced;
            return url;
        }

        private String removeSpecialChars(String url) {
            String specialRemoved = "";
            for (int i = 0; i < url.length(); i++) {
                if (url.charAt(i) != ',' && url.charAt(i) != ':' 
                        && url.charAt(i) != '.' && url.charAt(i) != '?') {
                    specialRemoved += url.charAt(i);
                }
            }
            url = specialRemoved;
            return url;
        }

        private String trimSpaces(String title) {
            String url = "";
            url = title.trim();
            return url;
        }
    }
    ```
