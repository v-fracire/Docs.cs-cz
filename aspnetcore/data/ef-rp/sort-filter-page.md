---
title: "Stránky Razor EF základní - řazení, filtru, stránkování - 3 8"
author: rick-anderson
description: "V tomto kurzu přidáte třídění, filtrování a stránkování funkce na stránku pomocí ASP.NET Core a Entity Framework Core."
keywords: "ASP.NET Core, Entity Framework Core, řazení, filtru, stránkování, seskupování"
ms.author: riande
ms.date: 10/22/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-rp/sort-filter-page
ms.openlocfilehash: 5e17663b88a622101245228e9372db55e4e874be
ms.sourcegitcommit: 703593d5fd14076e79be2ba75a5b8da12a60ab15
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2017
---
# <a name="sorting-filtering-paging-and-grouping---ef-core-with-razor-pages-3-of-8"></a>Řazení, filtrování, stránkování a seskupení – základní EF s stránky Razor (3 8)

Podle [tní Dykstra](https://github.com/tdykstra), [Rick Anderson](https://twitter.com/RickAndMSFT), a [Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE[about the series](../../includes/RP-EF/intro.md)]

Tento kurz, řazení, filtrování, seskupování a stránkování se přidá funkce.

Následující obrázek znázorňuje dokončené stránky. Záhlaví sloupců jsou odkazy, můžete kliknout na seřadí hodnoty ve sloupci. Kliknutím na záhlaví opakovaně sloupce přepíná mezi vzestupné a sestupné řazení.

![Studenti, kteří indexovou stránku](sort-filter-page/_static/paging.png)

Pokud narazíte na problémy, které nelze vyřešit, stáhněte si [dokončené aplikace pro tuto fázi](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting).

## <a name="add-sorting-to-the-index-page"></a>Přidání řazení indexovou stránku

Přidat řetězce *Students/Index.cshtml.cs* `PageModel` tak, aby obsahovala řazení paramaters:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet1&highlight=10-13)]


Aktualizace *Students/Index.cshtml.cs* `OnGetAsync` následujícím kódem:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly)]

Předchozí kód přijme `sortOrder` parametr z řetězce dotazu v adrese URL. Adresa URL (včetně řetězce dotazu) je generován [pomocná značka ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper
)

`sortOrder` Parametr je "Name" nebo "Data". `sortOrder` Parametru může volitelně následovat "_desc" Zadejte sestupném pořadí. Výchozí pořadí řazení je vzestupně.

Pokud se požaduje indexovou stránku z **studenty** propojit, neexistuje žádný řetězec dotazu. Studenti, kteří jsou zobrazeny ve vzestupném pořadí podle příjmení. Ve vzestupném pořadí podle příjmení je výchozí (případě patří prostřednictvím) `switch` příkaz. Když uživatel klikne na sloupec záhlaví odkaz, odpovídající `sortOrder` hodnota je součástí hodnotu řetězce dotazu.

`NameSort`a `DateSort` jsou stránky Razor použít ke konfiguraci hypertextové odkazy záhlaví sloupce s řetězcové hodnoty odpovídající dotazu:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=3-4)]

Následující kód obsahuje jazyka C# [?: – operátor](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/conditional-operator):

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_Ternary)]

První řádek určuje, kdy `sortOrder` má hodnotu null nebo prázdná, `NameSort` je nastaven na "name_desc." Pokud `sortOrder` je **není** hodnotu null nebo prázdná, `NameSort` nastavena na prázdný řetězec.

`?: operator` Je také označován jako Ternární operátor.

Tyto dva příkazy zapnutí zobrazení nastavit sloupec hypertextové odkazy záhlaví následujícím způsobem:

| Aktuální řazení | Poslední název hypertextový odkaz | Datum hypertextový odkaz |
|:--------------------:|:-------------------:|:--------------:|
| Poslední název vzestupné | descending        | ascending      |
| Poslední sestupném název | ascending           | ascending      |
| Datum vzestupné       | ascending           | descending     |
| Datum sestupném      | ascending           | ascending      |

Metoda používá k určení tento sloupec seřadit podle technologie LINQ to Entities. Inicializuje kód `IQueryable<Student> ` před příkazem switch a upravuje v příkazu přepínače:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=6-)]

 Když`IQueryable` vytvoření nebo úpravě, odešle žádný dotaz do databáze. Dokud není spustit dotaz `IQueryable` objekt je převeden do kolekce. `IQueryable`se převedou na kolekci voláním metody `ToListAsync`. Proto `IQueryable` kódu má za následek jeden dotaz, který není provést, dokud následující příkaz:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync`může získat podrobné s velkým počtem sloupců.

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a>Přidejte hypertextové odkazy záhlaví sloupců do zobrazení indexu studenty

Nahraďte kód v *Students/Index.cshtml*, s následujícími službami zvýrazněná kódu:

[!code-html[](intro/samples/cu/Pages/Students/Index2.cshtml?highlight=17-19,25-27)]

Předchozí kód:

* Přidá hypertextové odkazy na `LastName` a `EnrollmentDate` záhlaví sloupců.
* Používá informace v `NameSort` a `DateSort` nastavit hypertextových odkazů pomocí aktuálních hodnot pořadí řazení.

Ověření funkčnosti řazení:

* Spusťte aplikaci a vyberte **studenty** kartě.
* Klikněte na tlačítko **příjmení**.
* Klikněte na tlačítko **datum registrace**.

Pokud chcete získat lépe porozumět kód:

* V *Student/Index.cshtml.cs*, nastavit zarážky `switch (sortOrder)`.
* Přidat sledování pro `NameSort` a `DateSort`.
* V *Student/Index.cshtml*, nastavit zarážky `@Html.DisplayNameFor(model => model.Student[0].LastName)`.

Projděte ladicího programu.

## <a name="add-a-search-box-to-the-students-index-page"></a>Přidat vyhledávací pole na studenty indexovou stránku

Chcete-li přidat filtrování studenty indexovou stránku:

* Textové pole a tlačítko odeslání se přidá na stránku Razor. Textové pole poskytuje hledaný řetězec na jméno nebo příjmení.
* Použít hodnotu textového pole aktualizace souboru kódu na pozadí.

### <a name="add-filtering-functionality-to-the-index-method"></a>Přidat další filtrování funkce Index – metoda

Aktualizace *Students/Index.cshtml.cs* `OnGetAsync` následujícím kódem:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

Předchozí kód:

* Přidá `searchString` parametru `OnGetAsync` metoda. Hodnota řetězce vyhledávání byl přijat v textovém poli, který je přidán v další části.
* Přidat k příkazu LINQ `Where` klauzule. `Where` Klauzule vybere pouze studenty, jejichž křestní jméno nebo příjmení obsahuje řetězec pro hledání. Příkaz LINQ se spustí pouze v případě, že je hodnota pro vyhledávání.

Poznámka: Předchozí kód volání `Where` metodu `IQueryable` objekt a filtr zpracování na serveru. V některých scénářích může být aplikace zadaná volání `Where` metoda jako metody rozšíření na kolekci v paměti. Předpokládejme například, `_context.Students` změní z EF základní `DbSet` metodě úložiště, který vrací `IEnumerable` kolekce. Výsledek by za normálních okolností stejné, ale v některých případech může být odlišné.

Například rozhraní .NET Framework implementace `Contains` provádí malá a velká písmena porovnání ve výchozím nastavení. V systému SQL Server `Contains` rozlišování je určen podle nastavení kolace instance systému SQL Server. SQL používat výchozí hodnoty na velká a malá písmena. `ToUpper`může být volána aby test explicitně velká a malá písmena:

`Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`

Předchozí kód by zajistěte, aby byly výsledky velká a malá písmena Pokud kód změní na použití `IEnumerable`. Když `Contains` se volá na `IEnumerable` kolekce, .NET Core slouží k implementaci. Když `Contains` se volá na `IQueryable` objekt, je použít implementaci databáze. Návrat `IEnumerable` z úložiště může mít penality významně zvýšit výkon:

1. Ze serveru databáze jsou vráceny všechny řádky.
1. Se filtr použije na všechny řádky vrácené v aplikaci.

Je snížení výkonu pro volání `ToUpper`. `ToUpper` Kód přidá funkce v klauzuli WHERE příkazu TSQL SELECT. Přidaná funkce bránit v použití indexu okně Optimalizace. Vzhledem k tomu, že SQL je nainstalován velká a malá písmena, je vyhýbat se `ToUpper` volat, když není potřeba.

### <a name="add-a-search-box-to-the-student-index-view"></a>Vyhledávací pole, přidejte do zobrazení indexu studenty

V *Views/Student/Index.cshtml*, přidejte následující zvýrazněný kód k vytvoření **vyhledávání** tlačítko) a chrome (různé.

[!code-html[](intro/samples/cu/Pages/Students/Index3.cshtml?highlight=14-23&range=1-25)]

Předchozí kód používá `<form>` [značky pomocná](xref:mvc/views/tag-helpers/intro) do textového pole pro vyhledávání a tlačítko Přidat. Ve výchozím nastavení `<form>` značky pomocná odesílat data formuláře s příspěvku na. S POST jsou předávány parametry v textu zprávy HTTP a není v adrese URL. Pokud se používá HTTP GET, data formuláře je předán v adrese URL jako řetězce dotazu. Předání dat pomocí řetězce dotazu umožňuje uživatelům bookmark adresu URL. [W3C pokyny](https://www.w3.org/2001/tag/doc/whenToUseGet.html) doporučujeme, aby GET se mají použít při akci nevede aktualizaci.

Testovací aplikace:

* Vyberte **studenty** kartě a zadejte hledaný řetězec.
* Vyberte **vyhledávání**.

Všimněte si, že adresa URL obsahuje řetězec pro hledání.

```html
http://localhost:5000/Students?SearchString=an
```

Pokud stránky je aktuálně přihlášeni, záložka obsahuje adresu URL stránky a `SearchString` řetězec dotazu. `method="get"` v `form` značka je v tom, co způsobilo řetězec dotazu, který má být vygenerován.

Teď, když je vybraný odkaz řazení záhlaví sloupce, filtr z hodnoty **vyhledávání** dojde ke ztrátě pole. Hodnota filtru ztraceny vyřešen v další části.

## <a name="add-paging-functionality-to-the-students-index-page"></a>Přidání funkce stránkování pro studenty indexovou stránku

V této části `PaginatedList` třída se vytvoří pro podporu stránkování. `PaginatedList` Třídy používá `Skip` a `Take` příkazy k filtrování dat na serveru, místo načítání všechny řádky v tabulce. Následující obrázek znázorňuje tlačítka stránkování.

![Studenti, kteří indexu stránka s odkazy stránkování](sort-filter-page/_static/paging.png)

Ve složce projektu vytvořte `PaginatedList.cs` následujícím kódem:

[!code-csharp[Main](intro/samples/cu/PaginatedList.cs)]

`CreateAsync` Metoda v předchozí kód vezme velikost stránky a číslo stránky a použije příslušné `Skip` a `Take` příkazy `IQueryable`. Když `ToListAsync` se volá na `IQueryable`, vrací seznam obsahující pouze k požadované stránce. Vlastnosti `HasPreviousPage` a `HasNextPage` slouží k povolení nebo zakázání **předchozí** a **Další** stránkování tlačítka.

`CreateAsync` Metoda se používá k vytvoření `PaginatedList<T>`. Nelze vytvořit konstruktor `PaginatedList<T>` objektu konstruktory nelze spustit asynchronní kódu.

## <a name="add-paging-functionality-to-the-index-method"></a>Přidání funkce stránkování metodu indexu

V *Students/Index.cshtml.cs*, aktualizujte typ `Student` z `IList<Student>` k `PaginatedList<Student>`:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPageType)]

Aktualizace *Students/Index.cshtml.cs* `OnGetAsync` následujícím kódem:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage&highlight=1-4,7-14,41-)]

Předchozí kód přidá index stránky, aktuální `sortOrder`a `currentFilter` k označení metody.

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage2)]

Všechny parametry mají hodnotu null při:

* Stránce je volána z **studenty** odkaz.
* Uživatel nebyl klikli stránkování nebo řazení odkaz.

Při kliknutí na odkaz stránkování index proměnné stránky obsahuje číslo stránky pro zobrazení.

`CurrentSort`poskytuje stránky Razor aktuální pořadí řazení. Aktuální řazení musí být součástí odkazy stránkování zachovat pořadí řazení při stránkování.

`CurrentFilter`poskytuje stránky Razor aktuální řetězec filtru. `CurrentFilter` Hodnotu:

* Musí být součástí odkazy stránkování aby byla zachována nastavení filtru během stránkování.
* Musíte je obnovit do textového pole, pokud se zobrazí stránku znovu.

Pokud řetězec pro hledání se změní při stránkování, stránky se resetuje na hodnotu 1. Stránce musí obnovit na 1, protože nový filtr může mít za následek různé data k zobrazení. Pokud je zadána hodnota vyhledávání a **odeslání** je vybrán:

* Řetězec pro hledání se změní.
* `searchString` Parametr není null.

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage3)]

`PaginatedList.CreateAsync` Metoda převede student dotaz na jednu stránku studentů v typu kolekce, která podporuje stránkování. Této stránce studentů je předán na stránku Razor.

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage4)]

Dva otazníky v `PaginatedList.CreateAsync` představují [slučování null operátor](https://docs.microsoft.com/ dotnet/csharp/language-reference/operators/null-conditional-operator). Operátor slučování null definuje výchozí hodnotu pro typ s možnou hodnotou Null. Výraz `(pageIndex ?? 1)` znamená vrátí hodnotu `pageIndex` Pokud má hodnotu. Pokud `pageIndex` nebude mít hodnotu, vrátí 1.

## <a name="add-paging-links-to-the-student-razor-page"></a>Přidat stránkování odkazy na student stránky Razor

Aktualizovat kód v *Students/Index.cshtml*. Změny se zvýrazněnou:

[!code-html[](intro/samples/cu/Pages/Students/Index.cshtml?highlight=28-31,37-40,68-)]

Odkazy záhlaví sloupce používáte k průchodu aktuální hledaný řetězec na řetězec dotazu `OnGetAsync` metoda tak, aby uživatel může seřadit v rámci výsledky filtru:

[!code-html[](intro/samples/cu/Pages/Students/Index.cshtml?range=28-31)]

Tlačítka stránkování se zobrazí podle značky pomocné rutiny:

[!code-html[](intro/samples/cu/Pages/Students/Index.cshtml?range=72-)]

Spusťte aplikaci a přejděte na stránku studenty.

* Chcete-li se, funguje stránkování, kliknutím na odkazy stránkování v jiné pořadí řazení.
* Pokud chcete ověřit, že stránkování funguje správně s řazení a filtrování, zadejte hledaný řetězec a zkuste stránkování.

![studenti, kteří indexovou stránku s odkazy stránkování](sort-filter-page/_static/paging.png)

Pokud chcete získat lépe porozumět kód:

* V *Student/Index.cshtml.cs*, nastavit zarážky `switch (sortOrder)`.
* Přidat sledování pro `NameSort`, `DateSort`, `CurrentSort`, a `Model.Student.PageIndex`.
* V *Student/Index.cshtml*, nastavit zarážky `@Html.DisplayNameFor(model => model.Student[0].LastName)`.

Projděte ladicího programu.

## <a name="update-the-about-page-to-show-student-statistics"></a>O stránku zobrazíte student statistiky aktualizace

V tomto kroku *Pages/About.cshtml* se aktualizuje a zobrazí, kolik studenti, kteří mají zaregistrované pro každé datum registrace. Aktualizace používá seskupování a zahrnuje následující kroky:

* Vytvořte třídu modelu zobrazení dat používané **o** stránky.
* Upravte soubor o stránky Razor a kódu.

### <a name="create-the-view-model"></a>Vytvoření modelu zobrazení

Vytvoření *SchoolViewModels* složku *modely* složky.

V *SchoolViewModels* složky, přidejte *EnrollmentDateGroup.cs* následujícím kódem:

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="update-the-about-code-behind-page"></a>Aktualizace kódu stránky o

Aktualizace *Pages/About.cshtml.cs* soubor s následujícím kódem:

[!code-csharp[Main](intro/samples/cu/Pages/About.cshtml.cs)]

Příkaz LINQ skupiny entit student datu registrace, vypočítá počet entit v každé skupině a ukládá výsledky do kolekce `EnrollmentDateGroup` zobrazit objekty modelu.

Poznámka: LINQ `group` příkaz není aktuálně podporována EF jádra. V předchozím kódu jsou vráceny všechny záznamy studentů ze serveru SQL Server. `group` Příkaz se použije v aplikaci stránky Razor, není na serveru SQL. Základní EF 2.1 bude podporovat tento LINQ `group` operátor a seskupení probíhá na serveru SQL. V tématu [relační: Podpora překladu GroupBy() do SQL](https://github.com/aspnet/EntityFrameworkCore/issues/2341). [Základní EF 2.1](https://github.com/aspnet/EntityFrameworkCore/wiki/roadmap) s .NET Core 2.1, budou vydané. Další informace najdete v tématu [.NET Core plán](https://github.com/dotnet/core/blob/master/roadmap.md).

### <a name="modify-the-about-razor-page"></a>Upravit o stránky Razor

Nahraďte kód v *Views/Home/About.cshtml* soubor s následujícím kódem:

[!code-html[](intro/samples/cu/Pages/About.cshtml)]

Spusťte aplikaci a přejděte na stránku o. Počet studenty pro každé datum registrace se zobrazí v tabulce.

Pokud narazíte na problémy, které nelze vyřešit, stáhněte si [dokončené aplikace pro tuto fázi](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting).

![O stránku](sort-filter-page/_static/about.png)

## <a name="additional-resources"></a>Další prostředky

* [Ladění ASP.NET Core 2.x zdroje](https://github.com/aspnet/Docs/issues/4155)

V dalším kurzu aplikace používá migrace aktualizovat data modelu.

>[!div class="step-by-step"]
[Předchozí](xref:data/ef-rp/crud)
[další](xref:data/ef-rp/migrations)