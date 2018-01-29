---
title: Vazby modelu
author: rachelappel
description: "Informace o vazbě modelu v aplikaci ASP.NET MVC jádra"
ms.author: rachelap
manager: wpickett
ms.date: 01/22/2018
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
uid: mvc/models/model-binding
ms.openlocfilehash: 26c4c016548cc3e465991c5ebf16893d4022145d
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2018
---
# <a name="model-binding"></a>Vazby modelu

Podle [Rachel Appel](https://github.com/rachelappel)

## <a name="introduction-to-model-binding"></a>Úvod do modelu vazby

Vazby modelu v aplikaci ASP.NET MVC základní mapuje data z požadavků HTTP pro parametry metody akce. Parametry může být jednoduché typy, jako je například řetězce, celá čísla nebo obtékaných objektů, nebo mohou být komplexní typy. Toto je skvělé funkce MVC, protože mapování příchozích dat na protějšek je často opakovaných scénář, bez ohledu na velikost a složitost dat. MVC řeší tento problém tím, že poskytuje rychle abstrakci vazby, takže vývojáři nemusí zachovat přepisování trochu odlišná verze tohoto stejný kód v každé aplikace. Zápis vlastního textu na typ převaděče kód je zdlouhavé a tím zvyšuje i pravděpodobnost chyby.

## <a name="how-model-binding-works"></a>Jak funguje vazby modelu

Když MVC obdrží požadavek HTTP, nasměruje ho do určité akce metodu řadiče. Určuje, která metoda akce ke spuštění na základě toho, co je v datech trasy a hodnot z požadavku HTTP se váže k této metodě akce parametry. Zvažte například následující adresu URL:

`http://contoso.com/movies/edit/2`

Vzhledem k tomu, že šablona trasy vypadá to, `{controller=Home}/{action=Index}/{id?}`, `movies/edit/2` směruje `Movies` řadiče a jeho `Edit` metody akce. Přijme také volitelný parametr názvem `id`. Kód pro metodu akce by měl vypadat přibližně takto:

```csharp
public IActionResult Edit(int? id)
   ```

Poznámka: Řetězců v trasu adresy URL nejsou velká a malá písmena.

MVC se pokusí vytvořit vazbu data žádosti k parametrům akce podle názvu. MVC bude vyhledávat hodnoty pro každý parametr pomocí názvu parametru a názvy jeho veřejné nastavit vlastnosti. V předchozím příkladu je jedinou akcí parametr s názvem `id`, který MVC váže hodnotu s tímto názvem v hodnoty trasy. Kromě hodnoty trasy MVC vytvoří vazbu dat z různých částí žádosti a dělá to tak, aby sada. Níže je seznam zdrojů dat v pořadí, ve kterém vazby modelu vypadá prostřednictvím je:

1. `Form values`: Toto jsou hodnot formuláře, které přejděte v požadavku HTTP využívající metodu POST. (včetně požadavků jQuery POST).

2. `Route values`: Sada hodnot trasy poskytované [směrování](xref:fundamentals/routing)

3. `Query strings`: Část řetězec dotazu identifikátoru URI.

<!-- DocFX BUG
The link works but generates an error when building with DocFX
@fundamentals/routing
[Routing](xref:fundamentals/routing)
-->

Poznámka: Formuláře hodnoty, data trasy a dotazů řetězce se ukládají jako dvojice název hodnota.

Vzhledem k tomu, že vazby modelu žádali klíč s názvem `id` a není nic s názvem `id` v hodnot formuláře, přesune do vyhledávání pro tento klíč hodnoty trasy. V našem příkladu je nalezena shoda. Vázání a hodnota je převést na celé číslo 2. Stejné žádosti pomocí úpravy (řetězec id) by převést na řetězec "2".

Pokud v příkladu jednoduché typy. V MVC jednoduché typy jsou všechny .NET primitivní typ nebo typ pomocí převaděče typu řetězec. Kdyby parametru metody akce třídy, jako `Movie` typu, který obsahuje jednoduché a komplexní typy jako vhodně vlastností MVC model vazby budou nadále jej zpracovat. Reflexe a rekurze používá procházení vlastnosti komplexních typů hledání shody. Vazby modelu hledá vzoru *parameter_name.property_name* k vytvoření vazby na vlastnosti. Pokud se nenajde odpovídající hodnoty tohoto formuláře, pokusí se vytvořit vazbu pomocí název vlastnosti. Pro tyto typy jako `Collection` typy, vazby modelu hledá shod, který se *parameter_name [index]* nebo právě *[index]*. Model vazby vyhodnotí `Dictionary` podobně typy žádostí o *parameter_name [klíč]* nebo právě *[klíč]*, pokud jsou klíče jednoduché typy. Klíče, které jsou podporovány odpovídat názvům pole HTML a značky Pomocníci vygenerované stejný typ modelu. To umožňuje odezvy hodnoty tak, aby pole formuláře zůstaly vyplněný se vstupem uživatele pro jejich pohodlí, například když vázaných dat z vytvořit nebo upravit neproběhla úspěšně ověření.

V pořadí pro vazbu provést třídu musí mít veřejný výchozí konstruktor a člen vázat musí být veřejné vlastnosti s možností zápisu. Pokud vazba modelu se stane, že třída bude vytvořena pouze pomocí výchozí veřejný konstruktor, můžete nastavit vlastnosti.

Když vazbu parametru vazby modelu zastaví hledá hodnoty s tímto názvem a jeho přejde k další parametr vazby. Výchozí chování vazby modelu, jinak hodnota nastaví parametrů na výchozí hodnoty v závislosti na jejich typu:

* `T[]`: S výjimkou produktů pole typu `byte[]`, vazba nastaví parametry typu `T[]` k `Array.Empty<T>()`. Pole typu `byte[]` jsou nastaveny na `null`.

* Odkazové typy: Vazba vytvoří instanci třídy s výchozím konstruktorem bez nastavení vlastností. Však model vazby sady `string` parametry `null`.

* Typy s možnou hodnotou NULL: Typy s možnou hodnotou Null jsou nastaveny na `null`. V předchozím příkladu model vazby sady `id` k `null` vzhledem k tomu, že je typu `int?`.

* Typy hodnot: Použití hodnot Null typy hodnot typu `T` jsou nastaveny na `default(T)`. Vazby modelu bude třeba nastavit parametr `int id` na hodnotu 0. Zvažte použití ověření modelu nebo typy s možnou hodnotou Null, namísto spoléhání na výchozí hodnoty.

Pokud vazba se nezdaří, nebude MVC vyvolána chyba. Všechny akce, která přijímá vstup uživatele by se mělo zjistit `ModelState.IsValid` vlastnost.

Poznámka: Každý záznam v kontroleru `ModelState` vlastnost je `ModelStateEntry` obsahující `Errors` vlastnost. Je málokdy potřeba dotazování tuto kolekci sami. Místo nich se používá `ModelState.IsValid`.

Kromě toho existují některé speciální datové typy, které MVC nutné vzít v úvahu při provádění vazby modelu:

* `IFormFile`, `IEnumerable<IFormFile>`: Jeden nebo více odeslané soubory, které jsou součástí požadavku HTTP.

* `CancellationToken`: Sloužící ke zrušení aktivity v asynchronní řadiče.

Tyto typy mohou být vázány na parametry akce nebo vlastnosti na typ třídy.

Po dokončení vazby modelu [ověření](validation.md) dojde. Vazby modelu výchozí dobře funguje pro velká většina vývojové scénáře. Je také rozšiřitelný, pokud máte jedinečné potřeby předdefinované chování můžete přizpůsobit.

## <a name="customize-model-binding-behavior-with-attributes"></a>Přizpůsobení chování vazby modelu s atributy

MVC obsahuje několik atributů, které můžete použít k přímé jeho výchozí chování vazby modelu do jiného zdroje. Například můžete určit, zda vazby je vyžadováno pro vlastnost, nebo pokud ji by se nikdy nemělo stát vůbec pomocí `[BindRequired]` nebo `[BindNever]` atributy. Alternativně můžete přepsat výchozí zdroj dat a zadejte zdroj dat vazač modelu. Níže je seznam atributů vazby modelu:

* `[BindRequired]`: Tento atribut přidá chybu stavu modelu, pokud vazba nebyla vytvořena.

* `[BindNever]`: Informuje vazač modelu pro nikdy vazbu pro tento parametr.

* `[FromHeader]`, `[FromQuery]`, `[FromRoute]`, `[FromForm]`: Použijte tyto zadat zdroj přesný vazby, kterou chcete použít.

* `[FromServices]`: Tento atribut používá [vkládání závislostí](../../fundamentals/dependency-injection.md) pro svázání parametrů ze služeb.

* `[FromBody]`: Pomocí nakonfigurované formátovací moduly vázat data z textu požadavku. Formátovací modul je vybrána, založené na typ obsahu požadavku.

* `[ModelBinder]`: Používaná k přepsání výchozí vazač modelu, vazba zdroje a název.

Atributy jsou velmi užitečné nástroje, když potřebujete přepsat výchozí chování vazby modelu.

## <a name="bind-formatted-data-from-the-request-body"></a>Vytvoření vazby formátovaných dat z textu žádosti

Žádost o data mohou pocházet v různých formátech, včetně JSON, XML a mnohé další. Při použití atributu [FromBody] k označení, že chcete vazby parametru na data v textu požadavku, aplikace MVC používá nakonfigurované sadu formátovacích modulů pro zpracování dat požadavku na základě jeho obsahu typu. Ve výchozím nastavení aplikace MVC zahrnuje `JsonInputFormatter` třídy pro zpracování dat JSON, ale můžete přidat další formátovacích modulů pro zpracování XML a dalších vlastních formátů.

> [!NOTE]
> Může existovat maximálně jeden parametr na každou akci označených pomocí `[FromBody]`. ASP.NET Core MVC runtime deleguje je zodpovědností čtení datový proud požadavku k formátování. Jakmile datový proud požadavku je pro čtení pro parametr, není obecně možné číst datový proud požadavku znovu pro vazby dalších `[FromBody]` parametry.

> [!NOTE]
> `JsonInputFormatter` Je výchozí formátování a je založena na [Json.NET](https://www.newtonsoft.com/json).

Vybere vstupní formátování na základě technologie ASP.NET [Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html) záhlaví a typ parametru, pokud je atribut u ní zadáno jinak. Pokud chcete použít XML nebo jiného formátu je nutné ho nakonfigurovat v *Startup.cs* soubor, ale budete chtít nejdřív mají získat odkaz na `Microsoft.AspNetCore.Mvc.Formatters.Xml` pomocí nástroje NuGet. Spuštění kódu by měl vypadat přibližně takto:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddXmlSerializerFormatters();
   }
```

Kód na *Startup.cs* soubor obsahuje `ConfigureServices` metoda s `services` argument můžete použít k vytvoření služeb pro aplikace ASP.NET. V ukázce přidáváme formátovací modul XML jako služba, která bude poskytovat MVC pro tuto aplikaci. `options` Argument předaný `AddMvc` metoda umožňuje přidávat a spravovat filtry, formátování a další možnosti systému z MVC po spuštění aplikace. Následně použít `Consumes` atribut třídy kontroleru nebo metod akce pro práci s formátem chcete.

### <a name="custom-model-binding"></a>Vazby vlastní modelu

Vazby modelu můžete rozšířit vytvořením vlastní vazače modelů vlastní. Další informace o [vazby modelu vlastní](../advanced/custom-model-binding.md).