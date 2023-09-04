# Enum-klasser
Förra veckan såg vi några exempel på att lagra konstanta värden som statiska variabler i en klass. I äldre versioner av Java var det faktiskt vanligt att använda statiska variabler på detta sätt:
```java 
class Färg {
    public static final int SPADER = 1;
    public static final int HJÄRTER = 2;
    public static final int KLÖVER = 3;
    public static final int RUTER = 4;
}
 ```

Nu kan spelkortsklassen se ut så här
```java 
class Spelkort {
    private int färg;
    private int nummer;
    
    public Spelkort(int färg, int nummer) {
        this.färg = färg;
        this.nummer = nummer;
    }

    public int getFärg() {
        return färg;
    }

    public int getNummer() {
        return nummer;
    } 
}
 ```
...och ett nytt spelkort kan skapas så här:
```java 
public static void main(String[] args) {
   Spelkort spader_sju = new Spelkort(Färg.SPADER, 7);
}
 ```

Metoden har dock ett klart brist: konstanta numeriska värden associeras inte automatiskt med sina betydelser. Även om vi skulle ha kommit överens om att det numeriska värdet 1 motsvarar spader i en kortlek, returnerar det att begära färgen alltid bara ett heltal:
```java 
public static void main(String[] args) {
   Spelkort spader_sju = new Spelkort(Färg.SPADER, 7);
   System.out.println("Kortets färg: " + spader_sju.getFärg());
   System.out.println("Kortets nummer: " + spader_sju.getNummer());
}
 ```
Programmet skriver ut:
```java 
Kortets färg: 1
Kortets nummer: 7
 ```

En bättre lösning är att använda enum-klassen som introducerades i Java-version fem.
## Vad är en enum-klass?
Syftet med enum-klassen är att definiera en uppsättning värden. Ett typiskt exempel skulle vara kortlekens färger, väderstreck eller Hogwarts hus.
Klassen definieras med nyckelordet enum. I dess enklaste form räcker det att vi skriver de värden som ingår i uppsättningen inom klassdefinitionen, separerade med kommatecken:
```java 
enum Färg {
    SPADER, HJÄRTER, KLÖVER, RUTER
}
 ```

Nu kan vi ändra definitionen av spelkortsklassen så att färgen är av typen Färg, inte ett heltal. Detta begränsar också de möjliga värdena: kortets färg kan inte vara något annat än något av de fyra som definieras i enum-klassen Färg.
```java 
class Spelkort {
    private Färg färg;
    private int nummer;
    
    public Spelkort(Färg färg, int nummer) {
        this.färg = färg;
        this.nummer = nummer;
    }

    public Färg getFärg() {
        return färg;
    }

    public int getNummer() {
        return nummer;
    } 
}
 ```

Detta visas nu både när kortet skapas och när kortets färg skrivs ut:
```java 
public static void main(String[] args) {
   Spelkort spader_sju = new Spelkort(Färg.SPADER, 7);
   System.out.println("Kortets färg: " + spader_sju.getFärg());
   System.out.println("Kortets nummer: " + spader_sju.getNummer());
}
 ```
Nu skriver programmet ut:
```java 
Kortets färg: SPADER
Kortets nummer: 7
 ```
## Användning av enum-värden