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

Optimiza el código mejorando el rendimiento de un método que no sea eficiente y por ejemplo genere un cuello de botella.

???+ warning "Código mejorable..."

    - La variable `vatType` se comprueba con cada uno de los casos, lo que no es muy eficiente si se tiene que hacer para muchos datos.

    ``` java title="susbtitutealtorithm/Order.java" hl_lines=""
    public class Order {
        public float applyVAT (int vatType) {
            float result = 0;

            switch (vatType) {
                case 1:
                    result = 1.04f;
                    break;
                case 2:
                    result = 1.18f;
                    break;
                case 3:
                    result = 1.21f;
                    break;
                default:
                    result = -1;
                    break;
        }
        return result;
        }
    }
    ```

??? abstract "Refactorización"

    - Se ha sustituido cada caso del switch por una entrada del array, de tal forma que el valor de cada caso coincide con el índice del array. Así se puede devolver el valor que le corresponda de forma directa.

    ``` java title="susbtitutealtorithm/refactored/Order.java" hl_lines="3"
    public class Order {
        public float applyVAT (int vatType) {
            float result[] = {-1, 1.04f, 1.18f, 1.21f};

            if (vatType > 0 && vatType < result.length) {
                return result[vatType];
            }
            return -1;
        }
    }
    ```

## Extraer clase

Sacar parte del contenido de una clase a otra porque la clase original tenga demasiadas responsabilidades.

???+ warning "Código mejorable..."

    - Los campos relacionados con la tarjeta de crédito se pueden extraer a otra clase.

    ``` java title="extractclass/Customer.java" hl_lines="4-6"
    public class Customer {
        private String name;
        private String dni;
        private String creditCard;
        private Date creditCardDate;
        private int creditCardControlNumber;
        
        public Customer(String name, String dni) {
            this.name = name;
            this.dni = dni;
        }

        public boolean isCardExpired () {
            return creditCardDate.before(new Date());
        }

        public boolean isValid () {
            boolean result = false;
            // Some code here...
            return result;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public String getDni() {
            return dni;
        }

        public void setDni(String dni) {
            this.dni = dni;
        }

        public String getCreditCard() {
            return creditCard;
        }

        public void setCreditCard(String creditCard) {
            this.creditCard = creditCard;
        }

        public Date getCreditCardDate() {
            return creditCardDate;
        }

        public void setCreditCardDate(Date creditCardDate) {
            this.creditCardDate = creditCardDate;
        }

        public int getCreditCardControlNumber() {
            return creditCardControlNumber;
        }

        public void setCreditCardControlNumber(int creditCardControlNumber) {
            this.creditCardControlNumber = creditCardControlNumber;
        }

    }
    ```

En IntelliJ IDEA, seleccionar el método/variable y `Refactor > Rename`.

??? abstract "Refactorización"

    - Se crea la clase `CreditCard` con sus atributos y métodos.

    ``` java title="extractclass/refactored/Customer.java" hl_lines=""
    public class Customer {
        private String name;
        private String dni;
        private CreditCard creditCard;

        public Customer(String name, String dni) {
            this.name = name;
            this.dni = dni;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public String getDni() {
            return dni;
        }

        public void setDni(String dni) {
            this.dni = dni;
        }
    }
    ```

    ``` java title="extractclass/refactored/CreditCard.java" hl_lines=""
    public class CreditCard {
        private String creditCard;
        private Date creditCardDate;
        private int creditCardControlNumber;
        
        public CreditCard(String creditCard, Date creditCardDate, int creditCardControlNumber) {
            this.creditCard = creditCard;
            this.creditCardDate = creditCardDate;
            this.creditCardControlNumber = creditCardControlNumber;
        }

        public boolean isCardExpired () {
            return creditCardDate.before(new Date());
        }

        public boolean isValid () {
            boolean result = false;
            // Some code here...
            return result;
        }
        
        public Date getCreditCardDate() {
            return creditCardDate;
        }

        public void setCreditCardDate(Date creditCardDate) {
            this.creditCardDate = creditCardDate;
        }

        public int getCreditCardControlNumber() {
            return creditCardControlNumber;
        }

        public void setCreditCardControlNumber(int creditCardControlNumber) {
            this.creditCardControlNumber = creditCardControlNumber;
        }
    }
    ```

## Clase en línea

Refactorización contraria a la anterior. Introducir los atributos y métodos de una clase que por sí sola no tenga mucho sentido en otra clase.

???+ warning "Código mejorable..."

    - La clase `Sex` sólo tiene un atributo y no define ningún comportamiento.

    ``` java title="inlineclass/Person.java" hl_lines=""
    public class Person {
        private String name;
        private Date birth;
        private Sex sex;
        
        public Person(String name, Date birth, int sexCode) {
            this.name = name;
            this.birth = birth;
            this.sex = new Sex(sexCode);
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public Date getBirth() {
            return birth;
        }

        public void setBirth(Date birth) {
            this.birth = birth;
        }

        public Sex getSex() {
            return sex;
        }

        public void setSex(Sex sex) {
            this.sex = sex;
        }

        @Override
        public String toString() {
            return "Person [name=" + name + ", sex=" + sex + "]";
        }
    }
    ```

    ``` java title="inlineclass/Person.java" hl_lines=""
    public class Sex {
        public static final int MALE = 0;
        public static final int FEMALE = 1;
        private int sex;

        public Sex (int sex) {
            this.sex = sex;
        }

        public int getSex () {
            return sex;
        }

        @Override
        public String toString() {
            return Integer.toString(sex);
        }
    }
    ```

??? abstract "Refactorización"

    - Se introducen los atributos y métodos de `Sex` dentro de `Person`.

    ``` java title="inlineclass/refactored/Person.java" hl_lines=""
    public class Person {
        private String name;
        private Date birth;
        public static final int MALE = 0;
        public static final int FEMALE = 1;
        private int sex;

        
        public Person(String name, Date birth, int sexCode) {
            this.name = name;
            this.birth = birth;
            this.sex = sexCode;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public Date getBirth() {
            return birth;
        }

        public void setBirth(Date birth) {
            this.birth = birth;
        }

        public int getSex() {
            return sex;
        }

        public void setSex(int sex) {
            this.sex = sex;
        }

        @Override
        public String toString() {
            return "Person [name=" + name + ", sex=" + sex + "]";
        }
    }
    ```

## Ocultar Delegar

Evitar que un cliente (método principal por ejemplo) pueda acceder a una clase delegada a través de clases intermedias.

???+ warning "Código mejorable..."

    - Desde la clase `Main` se accede al dado `Die` que depende de `Player`.

    ``` java title="hidedelegate/Main.java" hl_lines="15"
    public class Main {
        private Player player;
        private Die die;
        
        public Main () {
            init();
        }
        
        private void init () {
            player = new Player();
            die = player.getDie();
        }
        
        public int roll () {
            return die.roll();
        }
    }
    ```

    ``` java title="hidedelegate/Player.java" hl_lines=""
    public class Player {
        private Die die;
        
        public Player () {
            this.die = new Die();
        }
        
        public int roll () {
            return die.roll();
        }

        public Die getDie() {
            return die;
        }
    }
    ```

    ``` java title="hidedelegate/Die.java" hl_lines=""
    public class Die {
        private Random random = new Random();
        
        public int roll() {
            return random.nextInt(6) + 1;
        }
    }
    ```

??? abstract "Refactorización"

    - La clase `Main` ya no tiene la dependencia de `Die`, que sólo está en `Player`.

    ``` java title="hidedelegate/refactored/Main.java" hl_lines="13"
    public class Main {
        private Player player;
        
        public Main () {
            init();
        }
        
        private void init () {
            player = new Player();
        }
        
        public int roll () {
            return player.roll();
        }
    }
    ```

    ``` java title="hidedelegate/refactored/Player.java" hl_lines=""
    public class Player {
        private Die die;
        
        public Player () {
            this.die = new Die();
        }

        public int roll() {
            return die.roll();
        }
    }
    ```

    ``` java title="hidedelegate/refactored/Die.java" hl_lines=""
    public class Die {
        private Random random = new Random();
        
        public int roll() {
            return random.nextInt(6) + 1;
        }
    }
    ```

## Introducir método externo

Añadir un método en la clase cliente para extender la funcionalidad de otra clase a la que no tenemos acceso.

???+ warning "Código mejorable..."

    - El método `improvePassword` completa un String de asteriscos si este es muy corto.

    ``` java title="introduceforeignmethod/PasswordChecker.java" hl_lines=""
    public class PasswordChecker {
        public String improvePassword (String password) {
            if (password.length() < 5) {
                return "****" + password + "****";
            } else {
                return password;
            }
        }
    }
    ```

En IntelliJ IDEA, seleccionar el método/variable y `Refactor > Rename`.

??? abstract "Refactorización"

    - Extendemos la funcionalidad de la clase `String` completando con el propio password dado la vuelta si este es muy corto.

    ``` java title="introduceforeignmethod/refactored/PasswordChecker.java" hl_lines=""
    public class PasswordChecker {
        public String improvePassword (String password) {
            if (password.length() < 5) {
                return makeItPalindrome(password);
            } else {
                return password;
            }
        }
        
        // We extend String functionality
        private static String makeItPalindrome (String password) {
            return new String(password + new StringBuilder(password)
                                                            .reverse()
                                                            .toString());
        }
    }
    ```

## Introducir extensión local

Extender una clase para darle una nueva funcionalidad.

???+ warning "Código mejorable..."

    - Suponemos que no tenemos acceso a `Conversor` para añadir nuevos métodos.

    ``` java title="introducelocalextension/Main.java" hl_lines=""
    public class Main {
        private Conversor conversor = new Conversor();
        
        public double convert (double amount) {
            return conversor.euro2Dollar(amount);
        }
    }
    ```

    ``` java title="introducelocalextension/Conversor.java" hl_lines=""
    public class Conversor {
        public double euro2Dollar (double qty) {
            return qty * 1.4d;
        }

        public double dollar2Euro (double qty) {
            return qty * 0.6d;
        }
    }
    ```

En IntelliJ IDEA, seleccionar el método/variable y `Refactor > Rename`.

??? abstract "Refactorización"

    - Creamos una subclase `CoolConversor` de `Conversor` con sus funciones y añadimos las nuevas que necesitemos.

    ``` java title="introducelocalextension/refactored/Main.java" hl_lines=""
    public class Main {
        private CoolConversor conversor = new CoolConversor();
        
        public double convert (double amount) {
            return conversor.euro2Dollar(amount);
        }
    }
    ```

    ``` java title="introducelocalextension/refactored/CoolConversor.java" hl_lines=""
    public class CoolConversor extends Conversor {
        public double euro2Pound (double qty) {
            return qty * 0.8d;
        }

        public double pound2Euro (double qty) {
            return qty * 1.3d;
        }
    }
    ```

    ``` java title="introducelocalextension/refactored/Conversor.java" hl_lines=""
    public class Conversor {
        public double euro2Dollar (double qty) {
            return qty * 1.4d;
        }

        public double dollar2Euro (double qty) {
            return qty * 0.6d;
        }
    }
    ```

## Reemplazar un dato con un objeto

Reemplazar un atributo de una clase por un objeto porque necesite que le dotemos de algún comportamiento más amplio.

???+ warning "Código mejorable..."

    - Campo `address` se queda corto en este caso porque necesitamos diferenciar la ciudad, código postal...

    ``` java title="replacedatawithobject/Customer.java" hl_lines="4"
    public class Customer {
        private String name;
        private String email;
        private String address;
        
        public Customer(String name, String email, String address) {
            this.name = name;
            this.email = email;
            this.address = address;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public String getEmail() {
            return email;
        }

        public void setEmail(String email) {
            this.email = email;
        }

        public String getAddress() {
            return address;
        }

        public void setAddress(String address) {
            this.address = address;
        }

        @Override
        public String toString() {
            return "Customer [name=" + name + ", email=" + email + ", address=" + address + "]";
        }
    }
    ```

??? abstract "Refactorización"

    - Se extrae `address` a un objeto para poder diferenciar las partes de la propia dirección.

    ``` java title="replacedatawithobject/refactored/Customer.java" hl_lines="4"
    public class Customer {
        private String name;
        private String email;
        private Address address;

        public Customer(String name, String email, Address address) {
            this.name = name;
            this.email = email;
            this.address = address;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public String getEmail() {
            return email;
        }

        public void setEmail(String email) {
            this.email = email;
        }

        public Address getAddress() {
            return address;
        }

        public void setAddress(Address address) {
            this.address = address;
        }

        @Override
        public String toString() {
            return "Customer [name=" + name + ", email=" + email + ", address=" + address + "]";
        }
    }
    ```

    ``` java title="replacedatawithobject/refactored/Address.java" hl_lines=""
    public class Address {
		private String street;
		private String po;
		private String city;
		
		public Address(String street, String po, String city) {
			this.street = street;
			this.po = po;
			this.city = city;
		}

		@Override
		public String toString() {
			return city;
		}
    }
    ```

## Encapsular colección

ES común que al añadir los *getters* de forma automática, se añada un get de una colección que no deberíamos devolver. Esta refactorización consiste en encapsular esa colección, ocultandándola para que esté disponible sólo a través de los métodos que nos interese.

???+ warning "Código mejorable..."

    - El método `getPlayers` devuelve la colección completa de jugadores y no debería ser así.

    ``` java title="encapsultacollection/Team.java" hl_lines="19"
    public class Team {
        private String name;
        private Date creation;
        private ArrayList<Player> players = new ArrayList<Player>();

        public Team(String name, Date creation) {
            this.name = name;
            this.creation = creation;
        }

        public String getName() {
            return name;
        }

        public Date getCreation() {
            return creation;
        }
        
        public ArrayList<Player> getPlayers() {
            return players;
        }

        public int totalPlayers() {
            return players.size();
        }
    }
    ```

??? abstract "Refactorización"

    - Se crean métodos `getPlayer`, `addPlayer` y `removePlayer` para gestionar la colección sin necesidad de devolverla.

    ``` java title="encapsultacollection/refactored/Team.java" hl_lines="19 23 27"
    public class Team {
        private String name;
        private Date creation;
        private ArrayList<Player> players = new ArrayList<Player>();

        public Team(String name, Date creation) {
            this.name = name;
            this.creation = creation;
        }

        public String getName() {
            return name;
        }

        public Date getCreation() {
            return creation;
        }
        
        public Player getPlayer (int index) {
            return players.get(index);
        }

        public void addPlayer (Player player) {
            players.add(player);
        }

        public void removePlayer (int index) {
            players.remove(index);
        }
        
        public int totalPlayers() {
            return players.size();
        }
    }
    ```

## Pull up

Subir atributos y métodos comunes entre clases hijas, a la superclase.

???+ warning "Código mejorable..."

    - El atributo `plate` (matrícula) y el método `start` están repetidos en las clases hijas.

    ``` java title="pullup/Vehicle.java" hl_lines=""
    public class Vehicle {
        protected String name;
    }
    ```

    ``` java title="pullup/Car.java" hl_lines="2 6"
    public class Car extends Vehicle {
        private String plate;
        private String trunk;
        private boolean isTrunkOpened;
        
        public void start() { ... }

        public boolean isTrunkOpen() {
            return isTrunkOpened;
        }
    }
    ```

    ``` java title="pullup/MotorBike.java" hl_lines="2 5"
    public class MotorBike extends Vehicle {
        private String plate;
        private String helmet;

        public void start() { ... }
    }
    ```

En IntelliJ IDEA, seleccionar el método/variable y `Refactor > Pull Members Up`.

??? abstract "Refactorización"

    - Se sube a la clase padre el atributo común `plate` y método común `start`.

    ``` java title="pullup/refactored/Vehicle.java" hl_lines="3 5"
    public class Vehicle {
        protected String name;
        protected String plate;

        public void start() { ... }
    }
    ```

    ``` java title="pullup/refactored/Car.java" hl_lines="2 6"
    public class Car extends Vehicle {
        private String trunk;
        private boolean isTrunkOpened;

        public boolean isTrunkOpen() {
            return isTrunkOpened;
        }
    }
    ```

    ``` java title="pullup/refactored/MotorBike.java" hl_lines="2 5"
    public class MotorBike extends Vehicle {
        private String helmet;
    }
    ```

## Push down

La refactorización contraria a la anterior. Ocurre cuando hay atributos o métodos de una superclase que no tienen sentido en todas las clases hijas. En ese caso, se mueven a las clases hijas que correspondan.

???+ warning "Código mejorable..."

    - En una clase hija `Bicycle` no tiene sentido los atributos de matrícula, seguro, ni el método arrancar.

    ``` java title="pushdown/Vehicle.java" hl_lines="3 4 6"
    public class Vehicle {
        protected String name;
        protected String plate;
        protected Insurance insurance;

        public void start() { ...  }
    }
    ```

En IntelliJ IDEA, seleccionar el método/variable y `Refactor > Push Members Down`.

??? abstract "Refactorización"

    - Los atributos y métodos se pasan a las clase que los utilicen.

    ``` java title="pushdown/refactored/Vehicle.java" hl_lines=""
    public class Vehicle {
        protected String name;
    }
    ```

    ``` java title="pushdown/refactored/Car.java" hl_lines="4 5 11"
    public class Car extends Vehicle {
        private String trunk;
        private boolean isTrunkOpened;
        protected String plate;
        protected Insurance insurance;

        public boolean isTrunkOpen() {
            return isTrunkOpened;
        }

        public void start() { ... }
    }
    ```

    ``` java title="pushdown/refactored/Bicycle.java" hl_lines=""
    public class Bicycle extends Vehicle {
        private String helmet;
    }
    ```

## Reemplazar array con objeto

Cuando tenemos un array simplemente para almacenar una serie de datos variados, se puede reemplazar por un objeto con dichos datos para hacer el código más legible.

???+ warning "Código mejorable..."

    - El array `pilotData` se usa para almacenar datos variados sobre el piloto.

    ``` java title="replacearraywithobject/Airplane.java" hl_lines="3 10-12"
    public class Airplane {
        private String model;
        private String pilotData[] = new String[3];

        public Airplane(String model) {
            this.model = model;
        }

        public void initPilot(String name, String license, int flightHours) {
            pilotData[0] = name;
            pilotData[1] = license;
            pilotData[2] = Integer.toString(flightHours);
        }

        @Override
        public String toString() {
            return "Airplane [model=" + model + ", pilot=" + pilotData[0] + "]";
        }
    }
    ```

??? abstract "Refactorización"

    - Se crea una clase `Pilot` cuyos atributos son los datos que se almacenaban en el array. 

    ``` java title="replacearraywithobject/refactored/Airplane.java" hl_lines="3 10"
    public class Airplane {
        private String model;
        private Pilot pilot;

        public Airplane(String model) {
            this.model = model;
        }

        public void initPilot(String name, String license, int flightHours) {
            pilot = new Pilot(name, license, flightHours);
        }

        @Override
        public String toString() {
            return "Airplane [model=" + model + ", pilot=" + pilot + "]";
        }
    }
    ```

    ``` java title="replacearraywithobject/refactored/Pilot.java" hl_lines=""
    public class Pilot {
        private String name;
        private String license;
        private int flightHours;

        public Pilot (String name, String license, int flightHours) {
            this.name = name;
            this.license = license;
            this.flightHours = flightHours;
        }

        public String toString() {
            return name;
        }
    }
    ```