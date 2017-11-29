---
title: "Přidání vyhledávání na stránky Razor jádro ASP.NET"
author: rick-anderson
description: "Ukazuje, jak přidat vyhledávání na stránky ASP.NET Core Razor"
keywords: "ASP.NET Core, hledání, stránky Razor"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/razor-pages/search
ms.openlocfilehash: 2ffb6f13a7303527444085d137d1acac02d7e0ef
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2017
---
# <a name="adding-search-to-an-aspnet-core-mvc-app"></a>Přidání do aplikace ASP.NET MVC základní vyhledávání

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

V tomto dokumentu je přidána vyhledávací funkci na indexovou stránku, která umožňuje vyhledávání filmy podle *genre* nebo *název*.

Aktualizovat indexovou stránku `OnGetAsync` metoda následujícím kódem:

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

První řádek `OnGetAsync` metoda vytvoří [LINQ](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/) dotazu a vyberte filmy:

```csharp
 var movies = from m in _context.Movie
              select m;
```

Dotaz je *pouze* definované v tomto okamžiku, má **není** byly spuštěny s databází.

Pokud `searchString` parametr obsahuje řetězec, filmy dotazu je změněno na filtrování na řetězec pro hledání:

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

`s => s.Title.Contains()` Kód [výrazu Lambda](https://docs.microsoft.com/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambdas se používají v na základě metod [LINQ](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/) dotazuje jako argumenty pro standardní dotaz operátor metody, jako [kde](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) metoda nebo `Contains` (používá se v předchozí kód). Dotazy LINQ nebudou provedeny, když jsou definovány nebo když jsou upraveny voláním metody (například `Where`, `Contains` nebo `OrderBy`). Místo toho je odložen spuštění dotazu. To znamená, že je zpožděno vyhodnocení výrazu, dokud jeho zjištěné hodnota je vstupní přes nebo `ToListAsync` metoda je volána. V tématu [provádění dotazu](https://docs.microsoft.com/dotnet/framework/data/adonet/ef/language-reference/query-execution) Další informace.

**Poznámka:** [obsahuje](https://docs.microsoft.com//dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) metoda běží v databázi, není v kódu jazyka C#. Rozlišování velkých a malých písmen na dotaz závisí na databázi a kolace. Na serveru SQL Server `Contains` mapuje [SQL LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql), což je malá a velká písmena. V SQLite s výchozí kolace, je velká a malá písmena.

Přejděte na stránku filmy a připojte řetězec dotazu, jako `?searchString=Ghost` na adresu URL (například `http://localhost:5000/Movies?searchString=Ghost`). Filtrované filmy jsou zobrazeny.

![Zobrazení indexu](search/_static/ghost.png)

Pokud na indexovou stránku se přidá následující šablonu trasy, řetězec pro hledání lze předat jako segment adresy URL (například `http://localhost:5000/Movies/ghost`).

```cshtml
@page "{searchString?}"
```

Předchozí omezení trasy umožňuje hledání názvu jako data trasy (segment adresy URL) místo jako hodnotu řetězce dotazu.  `?` v `"{searchString?}"` znamená to je parametr volitelný trasy.

![Index zobrazení s neodstraněných Wordu přidat adresu Url a vrácený film seznam dvou filmy, Ghostbusters a Ghostbusters 2](search/_static/g2.png)

Nelze však budou uživatelé chcete upravit adresu URL pro vyhledání film. V tomto kroku se přidá uživatelského rozhraní pro filtrování filmy. Pokud jste přidali dané omezení trasy `"{searchString?}"`, odeberte ji.

Otevřete *Pages/Movies/Index.cshtml* souboru a přidejte `<form>` značek zvýrazněných v následujícím kódem:

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

HTML `<form>` značku používá [pomocná značku formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper). Při odeslání formuláře je odeslán řetězec filtru *stránkách nebo filmy nebo Index* stránky. Uložte změny a testovat filtr.

![Index zobrazení s neodstraněných word zadali do textového pole Název filtru](search/_static/filter.png)

## <a name="search-by-genre"></a>Hledat podle genre

Přidat na následující zvýrazněnou vlastnosti, které chcete *Pages/Movies/Index.cshtml.cs*:

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-)]

`SelectList Genres` Obsahuje seznam žánry. To umožňuje uživateli vybrat genre ze seznamu.

`MovieGenre` Vlastnost obsahuje konkrétní genre vybere uživatele (například "západní").

Aktualizace `OnGetAsync` metoda následujícím kódem:

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Následující kód je dotaz LINQ, který načte všechny žánry z databáze.

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

`SelectList` z žánry vytvoří projekce odlišné žánry.

<!-- BUG in OPS
Tag snippet_selectlist's start line '75' should be less than end line '29' when resolving "[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]"

There is no start line.

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]
-->

```csharp
Genres = new SelectList(await genreQuery.Distinct().ToListAsync());
```

### <a name="adding-search-by-genre"></a>Přidání vyhledávání podle genre

Aktualizace *Index.cshtml* následujícím způsobem:

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Testování aplikací tak, že genre, název filmu a oběma.

>[!div class="step-by-step"]
[Předchozí: Aktualizace stránky](xref:tutorials/razor-pages/da1)
[Další: Přidání nové pole](xref:tutorials/razor-pages/new-field)