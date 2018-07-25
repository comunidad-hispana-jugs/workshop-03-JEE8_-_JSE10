**JEP 314: Additional Unicode Language-Tag Extensions example**


Java SE 9 soporta BCP 47 U language-tag extensions para `calendars (ca)` y `numbers (nu)`. Java 10 adiciona soporte para las extensiones adicionales:

	cu (currency type)
	fw (first day of week)
	rg (region override)
	tz (time zone)


Este ejemplo muestra como estas extensiones pueden ser usadas para combinar y crear entornos locales mucho más específicas. Este ejemplo esta diseñado para ser trabajado en JShell, pero si lo desea puede intentar hacerlo en un una clase de Java


Usted puede jecutar este ejemplo en su Local JShell o en linea. para mas información sobre JShell puede ir a:

https://github.com/AdoptOpenJDK/jdk9-jigsaw/tree/master/session-3-jshell

Para ejecutar Local JShell ejecute el siguiente comando

     jshell
     
Verifique que está utilizando la versión de JShell para Java 10:

    |  Welcome to JShell -- Version 10
    |  For an introduction type: /help intro


Para ejecutar JShell en linea ir a la siguiente URL

    https://tryjshell.org
    
Verifique que está utilizando la versión de JShell para Java 10:

    |  Welcome to JShell -- Version 10
    |  For an introduction type: /help intro
    
    
Una vez que esté en JShell, intente hacer los siguientes ejercicios



1. Crear una configuración regional (Locale) para su país utilizando el constructor de dos parámetros
	
	 Locale myLocale = new Locale("es","CO"); // `es` es para el idioma español y `CO` para el país Colombia
	 
	 El siguiente enlace es útil para conocer los valores de cada país https://www.iana.org/assignments/language-subtag-registry/language-subtag-registry
	 
2. Ejecute las siguientes declaraciones y tome nota de los resultados

		System.out.println(Currency.getInstance(myLocale)); // muesttra la moneda de su país
	 
		System.out.println(Calendar.getInstance(myLocale).getFirstDayOfWeek()); // muestra el primer día de la semana de su país: 1 para el domingo, 2 para el lunes, 3 para el miércoles y así sucesivamente.

		System.out.println(NumberFormat.getInstance(myLocale).format(123456789)); // muestra el estilo de formato numérico de su país
	
		System.out.println(DateFormat.getDateInstance(1,myLocale).format(new Date())); // muestra la fecha de su país
	
3. Ahora, trabaje en pareja, cada uno elija un idioma/país diferente, es mucho mejor si los países están en diferentes continentes,
cree una nueva Configuración regional (Locale), ejecute las mismas instrucciones del paso anterior y compare el resultado.

4. Finalmente vamos a usar el soporte de extensión agregado en Java 10, esta vez debe crear su configuración regional usando un `Local.Builder` como se muestra a continuación.
  Reemplace las letras xx y YY para el idioma / país que usó en el primer ejercicio, y luego ejecute las declaraciones del paso número 2.
 ** Nota: ** Si está realizando pruebas con Francia, debe cambiar las letras FR en `FRzzzz` para otro país, por ejm. `UKzzzz`,` COzzzz`, `BRzzzz`

		Locale myLocale  = new Locale.Builder().setLanguage("xx").setRegion("YY") .setExtension('u',"rg-FRzzzz").build();

5. El desafío final es definir un Configuración regional (Locale) para su país, que use un idioma diferente, con "Viernes" como el primer día de la semana, USD como moneda y usa dígitos javaneses. Debe usar las declaraciones en el paso número dos para probar su configuración regional.
  Puede usar el siguiente comando para verificar la configuración regional que ha creado:
 
		System.out.println(myLocale.getDisplayName());
