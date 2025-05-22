# Refactorización

## Introducción

Refactorizar es **cambiar el código mejorando su estructura** sin alterar su comportamiento.

> "Cualquiera puede escribir código que un ordenador pueda entender. Los buenos programadores son aquellos que escriben código que los humanos puedan entender." - Martin Fowler

A continuación se presentan diferentes técnicas para refactorizar código. En cada apartado encontrarás el código original y el refactorizado. Es posible y recomendable aplicar varias de las técnicas a un mismo código.


## Renombrar variables y métodos

Cambiar nombres de variables y métodos por otros adecuados para su contexto aporta una mayor legibilidad del código y aclara su propósito.

???+ warning "Código mejorable..."

    - No queda claro qué hace el método o qué almacenan las variables.

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

        public float convertBitcoinsToEuros (float bitcoins) {
            float euros = bitcoins * EUR_BTC_CHANGE_RATE;
            return euros;
        }
    }
    ```

!!! abstract "Recomendación"
    
    Como norma general a la hora de nombrar se suele utilizar:

    - **Sustantivos** para *variables*. Ej: `price`.
    - **Verbos** para *métodos*. Ej: `getPrice`.


## Encapsulación

Ocultar propiedades y métodos que no se usen desde fuera haciéndolos privados mejora las operaciones de acceso sin exponer detalles internos de la propia clase.

???+ warning "Código mejorable..."

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

???+ warning "Código mejorable..."

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

    - Se extrae a métodos independientes cada fragmento de código que resuelva una pequeña tarea.

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

## Introducir métodos

Reintroducir el código de un método dentro del método que lo llama para simplificar el código al prescindir de métodos demasiado simples u obvios. Hace el código más legible, simplifica las clases y evita la proliferación excesiva de métodos.

???+ warning "Código mejorable..."

    ``` java title="inlinemethod/UrlCleaner.java"
    public class UrlCleaner {

        public String clean(String title) {
            String url = trimSpaces(title);

            url = removeSpecialChars(url);
            url = replaceSpaces(url);
            url = url.toLowerCase();

            return url;
        }

        private String replaceSpaces(String url) {
            return url.replaceAll("[\\s]+", " ").replaceAll("[\\s]", "-");
        }

        private String removeSpecialChars(String url) {
            return url.replaceAll("[\\.\\:\\,\\?\\!\\_\\;]", "");
        }

        private String trimSpaces(String url) {
            return url.trim();
        }
    }
    ```

En IntelliJ IDEA, seleccionar el método/variable y `Refactor > Rename`.

??? abstract "Refactorización"

    - Se reducen todos los métodos a un único método.

    ``` java title="inlinemethod/refactored/UrlCleaner.java"
    public class UrlCleaner {
        public String clean (String title) {
            return title.trim()
                        .replaceAll("[\\.\\:\\,\\?\\!\\_\\;]", "")  // Replaces special chars
                        .replaceAll("[\\s]+"," ")                   // Replace duplicated spaces
                        .replaceAll("[\\s]","-");                   // Replace spaces with hyphen
        }
    }
    ```

## Convertir parámetros en objetos

Sustituir una lista de parámetros de un método por un objeto para simplificar su uso.

???+ warning "Código mejorable..."

    - El método `addItem` tiene demasiados parámetros.

    ``` java title="parameterobject/Order.java" hl_lines="4"
    public class Order {
        private Hashtable<String, Float> items = new Hashtable<String, Float>();

        public void addItem(Integer productID, String description, Integer quantity, Float price, Float discount) {
            items.put(productID + ": " + description, (quantity * price) - (quantity * price * discount));
        }

        public float calculateTotal() {
            float total = 0;
            Enumeration<String> keys = items.keys();

            while (keys.hasMoreElements()) {
                total = total + items.get(keys.nextElement());
            }
            return total;
        }
    }
    ```

En IntelliJ IDEA, seleccionar el método y `Refactor > Introduce Parameter Object`.

??? abstract "Refactorización"

    - Se crea una clase `OrderItem` que agrupa en sus atributos los antiguos parámetros.

    ``` java title="parameterobject/refactored/Order.java" hl_lines="4"
    public class Order {
        private Hashtable<String, Float> items = new Hashtable<String, Float>();
        
        public void addItem (OrderItem orderItem) {
            items.put(orderItem.getProductID() + ": " + orderItem.getDescription(), orderItem.totalItem());
        }
        
        public float calculateTotal () {
            float total = 0;
            Enumeration<String> keys = items.keys();
            
            while(keys.hasMoreElements()) {
                total  = total + items.get(keys.nextElement());
            }
            
            return total;
        }
    }
    ```

    ``` java title="parameterobject/refactored/OrderItem.java"
    public class OrderItem {
        private int productID;
        private String description;
        private int quantity;
        private float price;
        private float discount;

        public OrderItem(int productID, String description, int quantity, float price, float discount) {
            this.productID = productID;
            this.description = description;
            this.quantity = quantity;
            this.price = price;
            this.discount = discount;
        }

        public float totalItem () {
            return (quantity*price) - (quantity*price*discount);
        }

        public Integer getProductID() {
            return productID;
        }

        public String getDescription() {
            return description;
        }        
    }
    ```

## Reemplazar variable temporal con consulta

Extraer el valor de una variable temporal a un método para reutilizar expresiones y producir un código más legible.

???+ warning "Código mejorable..."

    ``` java title="replacetempwithquery/Student.java" hl_lines="11"
    public class Student {
        private String name;
        private boolean hasGoodAttitude;

        public Student(String name, boolean hasGoodAttitude) {
            this.name = name;
            this.hasGoodAttitude = hasGoodAttitude;
        }

        public float calculateAverage(float homework, float exam) {
            float mark = (homework + exam) / 2;

            if (hasGoodAttitude) {
                return mark + 1;
            } else {
                return mark;
            }
        }
    }
    ```

En IntelliJ IDEA, seleccionar la asignación y `Refactor > Extract Method`.

??? abstract "Refactorización"

    - En el método `mark` se extrae la operación que se le asignaba a la variable temporal.

    ``` java title="replacetempwithquery/refactored/Student.java" hl_lines="12 14 18-20"
    public class StudentRefactored {
        private String name;
        private boolean hasGoodAttitude;

        public Student(String name, boolean hasGoodAttitude) {
            this.name = name;
            this.hasGoodAttitude = hasGoodAttitude;
        }

        public float calculateAverage(float homework, float exam) {
            if (hasGoodAttitude) {
                return mark(homework, exam) + 1;
            } else {
                return mark(homework, exam);
            }
        }

        private float mark(float homework, float exam) {
            return (homework + exam) / 2;
        }
    }
    ```

## Introducir variable explicativa

Introducir variables para explicar una expresión compleja. Muy útil en condicionales.

???+ warning "Código mejorable..."

    - La condición es muy compleja y difícil de entender a simple vista.

    ``` java title="explainingvariable/Customer.java" hl_lines="13"
    class Customer {
        private String name;
        private int age;
        private float salary;
        
        public Customer(String name, int age, float salary) {
            this.name = name;
            this.age = age;
            this.salary = salary;
        }

        public float applyDiscount (float totalAmount) {
            if ((age > 17 && age < 66) && (salary - (salary * 0.2f)) < 1000f && totalAmount * 0.5 < 100) {
                return totalAmount * 0.9f;
            } else {
                return totalAmount;
            }
        }
    }
    ```

En IntelliJ IDEA, seleccionar la expresión a extraer y `Refactor > Introduce Variable`.

??? abstract "Refactorización"

    - Se extraen a variables cada una de las expresiones del condicional.

    ``` java title="explainingvariable/refactored/Customer.java" hl_lines="17"
    class Customer {
        private String name;
        private int age;
        private float salary;
        
        public Customer(String name, int age, float salary) {
            this.name = name;
            this.age = age;
            this.salary = salary;
        }
        
        public float applyDiscount (float totalAmount) {
            boolean isWorkingAge = (age > 17 && age < 66);
            boolean isLowSalary = (salary - (salary * 0.2f)) < 1000f;
            boolean isLittleAmount = totalAmount * 0.5 < 100;
            
            if (isWorkingAge && isLowSalary && isLittleAmount) {
                return totalAmount * 0.9f;
            } else {
                return totalAmount;
            }
        }
    }
    ```


## Separar variable temporal

Utilizar variables específicas para cada propósito del método en lugar de una temporal que acumule todas las operaciones. Así, cada variable tendrá una responsabilidad única y evitamos posibles efectos inesperados.

???+ warning "Código mejorable..."

    - La variable `temp` se usa como cajón de sastre para todos los cálculos.

    ``` java title="splittemporaryvariable/Invoice.java" hl_lines="4"
    public class Invoice {

        public float totalPrice (float price, float vat, float discount) {
            float temp = 0;
            temp = (vat * price) / 100;
            System.out.println("Applied vat: " + temp);

            temp = price + temp;
            System.out.println("Total with vat: " + temp);

            return temp - discount;
        }
    }
    ```

??? abstract "Refactorización"

    - Se crean variables específicas `appliedVat` y `priceWithVat` para cada operación.
    - Otro paso adidiconal sería extraer a un método específico la operación de cada variable.

    ``` java title="splittemporaryvariable/refactored/Invoice.java" hl_lines="4 7"
    public class Invoice {
	
        public float totalPrice (float price, float vat, float discount) {
            float appliedVat = (vat * 100) / price;		
            System.out.println("Applied vat: " + appliedVat);
            
            float priceWithVat = price + appliedVat;
            System.out.println("Total: " + priceWithVat);
            
            return priceWithVat - discount;
        }
    }
    ```

## Eliminar asignaciones a parámetros

Evitar que se asignen valores a un parámetro dentro de un método utilizando una variable local. Se evitan efectos inesperados y así cada parámetro tiene un propósito único.

???+ warning "Código mejorable..."

    - Se modifica los valores de los parámetros `examMark` y `homeworkMark` dentro del método.

    ``` java title="removeparameterassignment/Student.java" hl_lines=4 8"
    public class Student {
        public float evaluateTerm(float homeworkMark, float examMark, float attitude) {
            if (examMark < 5) {
                examMark = 1;
            }

            if (homeworkMark < 4) {
                homeworkMark = 1;
            }
            return (homeworkMark + examMark) / 2 + attitude;
        }
    }
    ```

??? abstract "Refactorización"

    - Se crea la variable local `finalMark` para no modificar los parámetros.

    ``` java title="removeparameterassignment/refactored/Student.java" hl_lines="3"
    public class Student {
        public float evaluateTerm(float homeworkMark, float examMark, float attitude) {
            float finalMark = 0;
            if (examMark < 5) {
                finalMark = 1;
            } else {
                finalMark = examMark;
            }

            if (homeworkMark < 4) {
                finalMark = finalMark + 1;
            } else {
                finalMark = finalMark + homeworkMark;
            }
            return (finalMark) / 2 + attitude;
        }
    }
    ```

## Reemplazar método con un objeto

Extraer un método complejo o extenso a una clase aparte para simplificarlo.

???+ warning "Código mejorable..."

    - El método `applyDiscount` es demasiado extenso y se extraerá a una clase.

    ``` java title="replacemethodwithmethodobject/Customer.java" hl_lines="17"
    public class Customer {

        private boolean isVip;
        private boolean isSpecial;
        private int type;

        public static final int NORMAL = 0;
        public static final int SPECIAL = 1;
        public static final int VIP = 2;

        public Customer(boolean isVip, boolean isSpecial, int type) {
            this.isVip = isVip;
            this.isSpecial = isSpecial;
            this.type = type;
        }

        public double applyDiscount(double price, double discount) {
            double finalPrice;
            double appliedVat;
            
            switch (getType()) {
            case Customer.NORMAL:
                appliedVat = 1.21f;
                break;
            case Customer.SPECIAL:
                appliedVat = 1.15f;
                break;
            case Customer.VIP:
                appliedVat = 1.04f;
                break;
            default:
                appliedVat = 1.21f;
                break;
            }

            if (price > 50 && isVip()) {
                finalPrice = price * 0.5;
            } else if (price > 10 && isSpecial()) {
                finalPrice = price * 0.1;
            } else {
                finalPrice = price;
            }

            return finalPrice * appliedVat - discount;
        }
        
        public int getType() {
            return type;
        }

        public boolean isVip() {
            return isVip;
        }

        public boolean isSpecial() {
            return isSpecial;
        }
    }
    ```

??? abstract "Refactorización"

    - Se ha creado la clase `DiscountCalculator` con el método `applyDiscount`, el que a su vez, se ha extraido a métodos.
    - Mejora adicional: Sustituir el `switch` por una jerarquía de clases en la que la llamada al tipo de cliente se resuelva mediante polimorfismo.

    ``` java title="replacemethodwithmethodobject/refactored/DiscountCalculator.java" hl_lines="16"
    public class DiscountCalculator {
        private double finalPrice;
        private double appliedVat;
        private double price;
        private Customer customer;
        private double discount;

        public DiscountCalculator(double price, Customer customer, double discount) {
            finalPrice = 0;
            appliedVat = 0;
            this.price = price;
            this.customer = customer;
            this.discount = discount;
        }

        public double applyDiscount() {
            return calculatePrice() * calculateVat() - discount;
        }

        private double calculateVat() {
            switch (customer.getType()) {
                case Customer.NORMAL:
                    return 1.21f;
                case Customer.SPECIAL:
                    return 1.15f;
                case Customer.VIP:
                    return 1.04f;
                default:
                    return 1.21f;
            }
        }

        private double calculatePrice() {
            if (price > 50 && customer.isVip()) {
                finalPrice = price * 0.5;
            } else if (price > 10 && customer.isSpecial()) {
                finalPrice = price * 0.1;
            } else {
                finalPrice = price;
            }

            return finalPrice;
        }
    }
    ```

## Descomponer condicional

Sustituir expresiones complejas de un condicional por una llamada a un método. Así, se reducen los métodos y se crean condiciones reutilizables más expresivas. 

Similar a la refactorización vista de **Introducir variable explicativa**, pero con un método.

???+ warning "Código mejorable..."

    ``` java title="decomposeconditional/Invoice.java" hl_lines="11"
    public class Invoice {
        private Customer customer;
        
        public Invoice (Customer customer) {
            this.customer = customer;
        }
        
        public float calculatePayment (float price, float discount, float vat) {
            float payment = 0;
            
            if (customer.getAge() < 18 || customer.getAge() > 65 ) {
                payment = price * discount * vat;
            }else {
                payment = price * vat;
            }
            
            return payment;
        }
    }
    ```

En IntelliJ IDEA, seleccionar la expresión condicional y `Refactor > Extract Method`.

??? abstract "Refactorización"

    - Se crea el método `canApplyDiscount` para sustituir las expresiones complejas anteriores.

    ``` java title="decomposeconditional/refactored/Invoice.java" hl_lines="12"
    public class Invoice {
        
        private Customer customer;
        
        public Invoice (Customer customer) {
            this.customer = customer;
        }
        
        public float calculatePayment (float price, float discount, float vat) {
            float payment = 0;
            
            if (canApplyDiscount() ) {
                payment = price * discount * vat;
            }else {
                payment = price * vat;
            }

            return payment;
        }

        private boolean canApplyDiscount() {
            return customer.getAge() < 18 || customer.getAge() > 65;
        }
    }
    ```

## Consolidar expresión condicional

Agrupar los bloques condicionales que comparten el mismo código reduciendo el tamaño de los métodos y evitando código duplicado.

???+ warning "Código mejorable..."

    - Varios bloques condicionales tienen exactamente el mismo código.

    ``` java title="consolidateconditional/Invoice.java" hl_lines="13 15 17"
    public class Invoice {

        private Customer customer;
        private int year;

        public Invoice(Customer customer, int year) {
            this.customer = customer;
            this.year = year;
        }

        public float calculateTotal(float subtotal, float vat) {
            if (customer.getAge() < 18)
                return 0;
            if (new GregorianCalendar().get(Calendar.YEAR) > year)
                return 0;
            if (subtotal < 0.5f)
                return 0;

            return subtotal * vat;
        }
    }
    ```

??? abstract "Refactorización"

    - El código compartido se agrupa bajo una misma condición múltiple.
    - Se aplica además la refactorización anterior **Descomponer condicional** para extraer a un método dicho condicional.

    ``` java title="consolidateconditional/refactored/Invoice.java" hl_lines="20"
        public class Invoice {

        private Customer customer;
        private int year;

        public Invoice(Customer customer, int year) {
            this.customer = customer;
            this.year = year;
        }

        public float calculateTotal(float subtotal, float vat) {
            if (isUselessToCharge(subtotal)) {
                return 0;
            } else {
                return subtotal * vat;
            }
        }

        private boolean isUselessToCharge(float subtotal) {
            return (customer.getAge() < 18) || (new Date().getYear() > year) || (subtotal < 0.5f);
        }
    }
    ```

## Consolidar fragmentos de condicional duplicados

Extraer código repetido en diferentes bloques de condicionales para evitar la duplicidad.

???+ warning "Código mejorable..."

    - Algunas operaciones de los bloques del condicional son exactamente iguales y se podrían dejar fuera de los condicionales para que se apliquen en ambos casos.

    ``` java title="consolidateduplicateconditional/Invoice.java" hl_lines="17 21"
    public class Invoice {
	
        private Customer customer;
        private float price;
        private int qty;
        
        public Invoice (Customer customer, float price, int qty) {
            this.customer = customer;
            this.price = price;
            this.qty = qty;
        }
        
        public float calculateTotal (float vat, float discount) {
            float subtotal = 0;
            if (customer.isVip()) {
                subtotal = (price * qty) - discount;
                subtotal = subtotal * (1 + (vat/100));
                return subtotal;
            } else {
                subtotal = (price * qty);
                subtotal = subtotal * (1 + (vat/100));
                return subtotal;
            }
        }
    }
    ```

En IntelliJ IDEA, seleccionar el método/variable y `Refactor > Rename`.

??? abstract "Refactorización"

    - La operación duplicada se extrae fuera de los condiciones, aplicándola así en todos los casos.

    ``` java title="consolidateduplicateconditional/refactored/Invoice.java" hl_lines="20"
    public class Invoice {
	
        private Customer customer;
        private float price;
        private int qty;
        
        public Invoice (Customer customer, float price, int qty) {
            this.customer = customer;
            this.price = price;
            this.qty = qty;
        }
        
        public float calculateTotal (float vat, float discount) {
            float subtotal = 0;
            if (customer.isVip()) {
                subtotal = (price * qty) - discount;
            } else {
                subtotal = (price * qty);
            }
            return subtotal * (1 + (vat/100));
        }
    }
    ```

## Eliminar bandera de control

Eliminar variables que se utilizan para controlar el flujo del programa y en su lugar utilizar *break*, *continue* o *return* para conseguir el mismo propósito.

???+ warning "Código mejorable..."

    - Existe una variable bandera `found` que sería interesante eliminar.

    ``` java title="removecontrolflag/Friends.java" hl_lines="9"
    public class Friends {
        private String[] friends;
        
        public Friends (String[] friends) {
            this.friends = friends;
        }
        
        public int indexOf (String friend) {
            boolean found = false; // Bandera
            int i = 0;
            
            while (i < friends.length && !found ) { 
                if (friends[i].equals(friend)) {
                    found = true;
                }
                i++;
            }
            
            if (found) {
                return (i-1);
            } else {
                return -1;
            }
        }
    }
    ```

En IntelliJ IDEA, seleccionar el método/variable y `Refactor > Rename`.

??? abstract "Refactorización"

    - Se podría sustituir la variable bandara con un `break` para salir del bucle, pero mucho mejor es hacerlo con el `return` devolviendo la posición directamente. 

    ``` java title="packet/removecontrolflag/Friends.java" hl_lines="11"
    public class Friends {
        private String[] friends;
        
        public Friends (String[] friends) {
            this.friends = friends;
        }
        
        public int indexOf (String friend) {
            for (int i = 0; i< friends.length; i++) 
                if (friends[i].equals(friend)) 
                    return i;
            
            return -1;
        }
    }
    ```

## Reemplazar condicionales anidados con claúsulas guard

Simplificar los condicionales anidados llegando idealmente a un único nivel para facilitar su legibilidad.

Una **cláusula guard** (también llamada "early return" o "return early") es una técnica de programación que permite evitar la anidación excesiva de estructuras condicionales (if-else) en el código. Sirve para hacer que el código sea más legible y fácil de entender, al "filtrar" las condiciones al principio de una función y retornar antes si alguna condición no se cumple.

???+ warning "Código mejorable..."

    - Hay demasiados niveles if-else anidados que dificultan su comprensión.

    ``` java title="replacenestedconditionalguardclauses/Flight.java" hl_lines="14-26"
    public class Flight {
	
        private static final float CHILDREN_DISCOUNT = 0.9f;
        private static final float UNEMPLOYED_DISCOUNT = 0.8f;
        private static final int BASE_PRICE = 20;
        private int distance;
        
        public Flight (int distance) {
            this.distance = distance;
        }

        public float priceForPassenger (Passenger passenger) {
            float price = 0;
            if (passenger.isAChild()) {
                price = getChildDiscount();
            } else {
                if (passenger.isUnemployed()) {
                    price = getUnemployedDiscount();
                } else {
                    if (isChristmas()) {
                        price = 0;
                    } else {
                        price = getNormalPrice();
                    }
                }
            }
            return price;
        }

        private float getUnemployedDiscount() {
            return BASE_PRICE * distance * UNEMPLOYED_DISCOUNT;
        }

        private float getNormalPrice() {
            return BASE_PRICE * distance;
        }

        private boolean isChristmas() {
            return false;
        }

        private float getChildDiscount() {
            return BASE_PRICE * distance * CHILDREN_DISCOUNT;
        }
    }
    ```

??? abstract "Refactorización"

    - Se eliminan los if-else anidados dejándolos en un único nivel.

    ``` java title="replacenestedconditionalguardclauses/refactored/Flight.java" hl_lines="13-15"
    public class Flight {
	
        private static final float CHILDREN_DISCOUNT = 0.9f;
        private static final float UNEMPLOYED_DISCOUNT = 0.8f;
        private static final int BASE_PRICE = 20;
        private int distance;
        
        public Flight (int distance) {
            this.distance = distance;
        }

        public float priceForPassenger (Passenger passenger) {
            if (passenger.isAChild()) return getChildDiscount();
            if (passenger.isUnemployed()) return getUnemployedDiscount();
            if (isChristmas()) return 0;
            return getNormalPrice();

        }

        private float getUnemployedDiscount() {
            return BASE_PRICE * distance * UNEMPLOYED_DISCOUNT;
        }

        private float getNormalPrice() {
            return BASE_PRICE * distance;
        }

        private boolean isChristmas() {
            return false;
        }

        private float getChildDiscount() {
            return BASE_PRICE * distance * CHILDREN_DISCOUNT;
        }
    }
    ```

## Reemplazar condicional con polimorfismo

Sustituir las expresiones condicionales (normalmente un `switch`) por una jerarquía de clases en la que la llamada al tipo específico se resuelva mediante polimorfismo. 

De esta forma se mejora el mantenimiento de la aplicación al ser más sencillo añadir nuevos casos en un futuro sin necesidad de duplicar código.

???+ warning "Código mejorable..."

    - El `switch` se haría muy enrevesado si se tienen muchos tipos de vehículos.

    ``` java title="replaceconditionalwithpolymorphism/Vehicle.java" hl_lines="19-29"
    public class Vehicle {

        private static final int CAR = 0;
        private static final int BIKE = 1;
        private static final int PLANE = 2;
        
        private int vehicleType;
        private int speed;
        private int acceleration;

        public Vehicle(int vehicleType, int speed, int acceleration) {
            this.vehicleType = vehicleType;
            this.speed = speed;
            this.acceleration = acceleration;
        }
        
        public int move () {
            int result = 0;
            switch (vehicleType) {
                case CAR:
                    result = speed * acceleration * 5;
                    break;
                case BIKE:
                    result = speed * 10;
                    break;
                case PLANE:
                    result = acceleration * 2;
                    break;
            }

            return result;
        }
    }
    ```

??? abstract "Refactorización"

    - Se crea una clase abstracta `Vehiculo` con su método abstracto `move`.
    - Se crean clases hijas `Bike`, `Car` y `Plane` que definen dicho método `move` al que se llamará directamente si necesidad del `switch`.

    ``` java title="replaceconditionalwithpolymorphism/refactored/Vehicle.java" hl_lines="12"
    public abstract class Vehicle {
        protected int vehicleType;
        protected int speed;
        protected int acceleration;

        public Vehicle(int vehicleType, int speed, int acceleration) {
            this.vehicleType = vehicleType;
            this.speed = speed;
            this.acceleration = acceleration;
        }
        
        public abstract int move ();
    }
    ```

    ``` java title="replaceconditionalwithpolymorphism/refactored/Bike.java" hl_lines="7"
    public class Bike extends Vehicle {	
        public Bike(int vehicleType, int speed, int acceleration) {
            super(vehicleType, speed, acceleration);
        }
        
        @Override
        public int move () { return speed * 10; }
    }
    ```

    ``` java title="replaceconditionalwithpolymorphism/refactored/Car.java" hl_lines="7"
    public class Car extends Vehicle {
        public Car(int vehicleType, int speed, int acceleration) {
            super(vehicleType, speed, acceleration);
        }
        
        @Override
        public int move () { return speed * acceleration * 5; }
    }
    ```

    ``` java title="replaceconditionalwithpolymorphism/refactored/Plane.java" hl_lines="7"
    public class Plane extends Vehicle {
	    public Plane(int vehicleType, int speed, int acceleration) {
            super(vehicleType, speed, acceleration);
        }
        
        @Override
        public int move () { return  acceleration * 2; }
    }
    ```

## Introducir objeto nulo

Utilizar un objeto que representa el valor `null` de algo evitando realizar comparaciones directamente con `null`, lo que no suele ser muy recomendable porque puede provocar errores inesperados.

???+ warning "Código mejorable..."

    - Para el ataque del guerrero se comprueba que tenga un arma (no sea `null`) para obtener el daño que hace y sumarlo.
  
    ``` java title="introducenullobject/Weapon.java"
    public class Weapon {
        private int damage;

        public Weapon(int damage) {
            this.damage = damage;
        }

        public int getDamage() {
            return damage + new Random().nextInt(3);
        }
    }
    ```

    ``` java title="introducenullobject/Warrior.java" hl_lines="10"
    public class Warrior {
        private Weapon weapon;

        public Warrior(Weapon weapon) {
            this.weapon = weapon;
        }

        public int attack() {
            int damage = 0;
            if (weapon == null) {
                damage = 2;
            } else {
                damage = 2 + weapon.getDamage();
            }
            return damage;
        }
    }
    ```

??? abstract "Refactorización"

    - Se crea la clase `NullWeapon` que hereda de `Weapon` y representa el daño que hace no tener un arma equipada, evitando así la comparacióin con `null` anterior.

    ``` java title="introducenullobject/refactored/Warrior.java"
    public class NullWeapon extends Weapon {
        public NullWeapon(int damage) {
            super(damage);
        }

        @Override
        public int getDamage() {
            return 0;
        }
    }
    ```

    ``` java title="introducenullobject/Warrior.java" hl_lines="9"
    public class Warrior {
        private Weapon weapon;

        public Warrior(Weapon weapon) {
            this.weapon = weapon;
        }

        public int attack() {
            return 2 + weapon.getDamage();
        }
    }
    ```

## Separar consulta de modificación

Evitar que un mismo método haga una consulta de datos y relice modificaciones. Mejor separar en varios métodos para evitar que un método de consulta tenga resultados inesperados. Un método tiene que tener un único propósito.

???+ warning "Código mejorable..."

    - El método `initVehicleAndGetType` modifica los atributos `horsePower` y `type`. Y además devuelve `type`. Un método sólo debería hacer una única tarea, en este caso o modificar, o bien obtener valores.

    ``` java title="separatequerymodify/Vehicle.java" hl_lines="9-21"
    public class Vehicle {
        private int horsePower;
        private String type;

        public Vehicle(int power) {
            initVehicleAndGetType(power);
        }

        private String initVehicleAndGetType(int power) {
            horsePower = power;

            if (power >= 10) {
                type = "Truck";
            } else if (power > 5 && power < 10) {
                type = "Car";
            } else {
                type = "Bike";
            }
            
            return type;
        }

        public int getHorsePower() {
            return horsePower;
        }

        public String getType() {
            return type;
        }
    }
    ```

??? abstract "Refactorización"

    - Se modifica el método de inicio `init` estableciendo en métodos separados los atributos.
    - Se hacen los getters correspondientes para obtener el valor de los atributos.

    ``` java title="separatequerymodify/refactored/Vehicle.java"
    public class Vehicle {
        private int horsePower;
        private String type;

        public Vehicle(int power) {
            init(power);
        }

        private void init(int power) {
            setPower(power);
            setType();
        }

        private void setPower(int power) {
            horsePower = power;
        }

        private void setType() {
            if (horsePower >= 10) {
                type = "Truck";
            } else if (horsePower > 5 && horsePower < 10) {
                type = "Car";
            } else {
                type = "Bike";
            }
        }

        public int getHorsePower() {
            return horsePower;
        }

        public String getType() {
            return type;
        }
    }
    ```

## Parametrizar método

Unificar en un método varios métodos en los que sólo cambia un valor para evitar código duplicado. Se hace, añadiendo al método un parámetro (el valor que cambia).

???+ warning "Código mejorable..."

    - Hay varios métodos que hacen lo mismo cambiando un único valor.

    ``` java title="parametrizemethod/Invoice.java" hl_lines="20 25 30"
    public class Invoice {
        private float subtotal;
        private Customer customer;

        public Invoice(float subtotal, Customer customer) {
            this.subtotal = subtotal;
            this.customer = customer;
        }

        public float charge() {
            if (customer.getAge() < 18) {
                return chargeWithUnderageDiscount();
            } else if (customer.payInCash()) {
                return chargeWithCashDiscount();
            } else {
                return chargeNormal();
            }
        }

        private float chargeWithUnderageDiscount() {
            float total = subtotal * 0.5f;
            return total;
        }

        private float chargeWithCashDiscount() {
            float total = subtotal * 0.8f;
            return total;
        }

        private float chargeNormal() {
            return subtotal;
        }
    }
    ```

??? abstract "Refactorización"

    - Se añade como parámetro del método el valor que cambia unificando todo en un único método.

    ``` java title="parametrizemethod/refactored/Invoice.java" hl_lines="20"
    public class Invoice {
        private float subtotal;
        private Customer customer;

        public Invoice(float subtotal, Customer customer) {
            this.subtotal = subtotal;
            this.customer = customer;
        }

        public float charge() {
            if (customer.getAge() < 18) {
                return charge(0.5f);
            } else if (customer.payInCash()) {
                return charge(0.8f);
            } else {
                return charge();
            }
        }

        public float charge (float discount) {
            return subtotal * discount;
            }
    }
    ```

## Reemplazar parámetro con método explícito

La contraria a la refactorización anterior. En este caso reemplazamos un parámetro por métodos específicos. Se suele utilizar en métodos que han crecido mucho o realizan varias acciones.

???+ warning "Código mejorable..."

    - El método `initVehicle` ha crecido mucho con condicionales dentro para modificar varios atributos.

    ``` java title="replaceparameterwitexplicitmethod/Vehicle.java" hl_lines="11"
    public class Vehicle {

        private int acceleration;
        private int speed;
        
        public Vehicle(int acceleration, int speed) {
            this.acceleration = acceleration;
            this.speed = speed;
        }

        public void initVehicle (int type, int value) {
            if (type == 1) {
                acceleration = value;
                return;
            }

            if (type == 2 || type == 3)  {
                speed = value;
                return;
            }
        }

        public int getAcceleration() {
            return acceleration;
        }

        public int getSpeed() {
            return speed;
        }
    }
    ```

??? abstract "Refactorización"

    - El método `initVehicle` se ha separado en los setters correspondientes a los atributos, simplificando así el código.

    ``` java title="replaceparameterwitexplicitmethod/refactored/Vehicle.java" hl_lines="19 23"
    public class Vehicle {

        private int acceleration;
        private int speed;
        
        public Vehicle(int acceleration, int speed) {
            this.acceleration = acceleration;
            this.speed = speed;
        }
        
        public int getAcceleration() {
            return acceleration;
        }

        public int getSpeed() {
            return speed;
        }

        public void setAcceleration(int acceleration) {
            this.acceleration = acceleration;
        }
        
        public void setSpeed(int speed) {
            this.speed = speed;
        }
    }
    ```

## Sustituir algoritmo

Definición

???+ warning "Código mejorable..."

    ``` java title="packet/Class.java" hl_lines=""

    ```

En IntelliJ IDEA, seleccionar el método/variable y `Refactor > Rename`.

??? abstract "Refactorización"

    - acciones

    ``` java title="packet/refactored/Class.java" hl_lines=""

    ```