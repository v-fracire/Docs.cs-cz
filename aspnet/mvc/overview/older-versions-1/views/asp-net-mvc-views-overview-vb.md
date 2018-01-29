---
uid: mvc/overview/older-versions-1/views/asp-net-mvc-views-overview-vb
title: "ASP.NET MVC zobrazení přehled (VB) | Microsoft Docs"
author: StephenWalther
description: "Co je zobrazení ASP.NET MVC a jak ho se liší od stránku HTML? V tomto kurzu Stephen Walther vás seznámí s zobrazení a ukazuje, jak můžete t..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/16/2008
ms.topic: article
ms.assetid: c28ba88d-3a93-47f5-a306-049bd766714d
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/views/asp-net-mvc-views-overview-vb
msc.type: authoredcontent
ms.openlocfilehash: c85b969aa4457d0326b4a16da218db9e11d01e10
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2017
---
<a name="aspnet-mvc-views-overview-vb"></a>Zobrazení ASP.NET MVC (VB) – přehled
====================
podle [Stephen Walther](https://github.com/StephenWalther)

> Co je zobrazení ASP.NET MVC a jak ho se liší od stránku HTML? V tomto kurzu Stephen Walther vás seznámí s zobrazení a ukazuje, jak můžete využít zobrazení dat a pomocné objekty HTML v rámci zobrazení.


Účelem tohoto kurzu je poskytnout stručný úvod do architektury ASP.NET MVC zobrazení, data zobrazení a pomocné rutiny HTML. Na konci tohoto kurzu musíte vědět, jak vytvořit nové zobrazení, předat data z řadiče zobrazení a použití pomocné objekty HTML ke generování obsahu v zobrazení.

## <a name="understanding-views"></a>Principy zobrazení

Na rozdíl od Active Server Pages nebo ASP.NET rozhraní ASP.NET MVC nezahrnuje všechny položky, které odpovídá přímo na stránku. V aplikaci ASP.NET MVC není na stránce na disk, který odpovídá cestě v adrese URL, kterou zadáte do panelu Adresa prohlížeče. Je nejblíže si na stránce v aplikaci ASP.NET MVC je něco názvem *zobrazení*.

V aplikaci ASP.NET MVC jsou příchozí požadavky na prohlížeč namapované na akce kontroleru. Akce kontroleru může vrátit zobrazení. Akce kontroleru se ale může provést jiný typ akce, jako je přesměrování na jiný akce kontroleru.

Výpis 1 obsahuje řadič jednoduché s názvem HomeController. HomeController zpřístupní s názvem Index() a Details() dvě akce kontroleru.

**Výpis 1 - HomeController.vb**

[!code-vb[Main](asp-net-mvc-views-overview-vb/samples/sample1.vb)]

Je první akcí Index() akce, můžete vyvolat zadáním následující adresu URL do adresního řádku prohlížeče:

/ Home nebo Index

Druhou akci Details() akce, můžete vyvolat tak, že do prohlížeče zadáte tuto adresu:

/ Home/podrobnosti

Akce Index() vrátí zobrazení. Většinu akcí, které vytvoříte vrátí zobrazení. Akce však může vrátit jiné typy výsledky akce. Například Details() akce vrátí RedirectToActionResult, který přesměruje příchozí požadavek na akci Index().

Akce Index() obsahuje následující jediný řádek kódu:

View()

Tento řádek kódu vrátí zobrazení, které musí být v následujícím umístění na vašem webovém serveru:

\Views\Home\Index.aspx

Cesta k zobrazení je odvozeno z názvu řadiče a názvu akce kontroleru.

Pokud dáváte přednost, může být explicitní o zobrazení. Následující kód vrátí zobrazení s názvem František:

Zobrazení (František)

Po provedení tohoto řádku kódu zobrazení vrácených následující cestu:

\Views\Home\Fred.aspx

> [!NOTE] 
> 
> Pokud máte v plánu vytvářet testy částí pro aplikace ASP.NET MVC je vhodné být explicitní o názvech zobrazení. Tímto způsobem můžete vytvořit testů jednotek k ověření, že očekávané zobrazení vrátil akce kontroleru.


## <a name="adding-content-to-a-view"></a>Přidávání obsahu do zobrazení

Zobrazení je standard (dokumentu HTML, která může obsahovat skripty X). Pomocí skriptů dynamický obsah, přidejte do zobrazení.

Zobrazení v výpis 2 se například zobrazí aktuální datum a čas.

**Výpis 2 - \Views\Home\Index.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-vb/samples/sample2.aspx)]

Všimněte si, že tělo stránky HTML v výpis 2 obsahuje následující skript:

&lt;% Response.Write(DateTime.Now) %&gt;

Použít oddělovače skriptu &lt;% a %&gt; označit začátek a konec skriptu. Tento skript je napsán v jazyce Visual basic. Zobrazí aktuální datum a čas voláním metody Response.Write() k vykreslení obsahu v prohlížeči. Oddělovače skriptu &lt;% a %&gt; lze použít k provedení jednoho nebo více příkazů.

Vzhledem k tomu, že zavoláte Response.Write() tak často, Microsoft vám poskytne zástupce pro volání metody Response.Write(). Zobrazení v výpis 3 používá oddělovače &lt;% = a %&gt; jako zástupce pro volání Response.Write().

**Výpis 3 - Views\Home\Index2.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-vb/samples/sample3.aspx)]

Kterémkoli jazyce platformy .NET můžete použít ke generování dynamický obsah v zobrazení. Za normálních okolností udou můžete použít buď Visual Basic .NET nebo C# k zápisu kontrolery a zobrazení.

## <a name="using-html-helpers-to-generate-view-content"></a>Použití pomocných rutinách HTML pro generování zobrazení obsahu

Aby bylo snazší přidání obsahu do zobrazení, můžete využít výhod takzvaný *pomocné rutiny HTML*. Pomocné rutiny HTML, je obvykle metodu, která generuje řetězec. Pomocné rutiny HTML můžete použít ke generování standardní elementů HTML například textových polí, odkazů, rozevírací seznamy a seznamy.

Například zobrazení v výpis 4 trvá výhod tři pomocné rutiny HTML--BeginForm(), TextBox() a Password() pomocníky – ke generování přihlášení formuláři (viz obrázek 1).

**Výpis 4 – \Views\Home\Login.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-vb/samples/sample4.aspx)]


[![Dialogové okno Nový projekt](asp-net-mvc-views-overview-vb/_static/image1.jpg)](asp-net-mvc-views-overview-vb/_static/image1.png)

**Obrázek 01**: standardní přihlašovací formulář ([Kliknutím zobrazit obrázek v plné velikosti](asp-net-mvc-views-overview-vb/_static/image2.png))


Všechny metody pomocné rutiny HTML, se nazývají na vlastnost Html zobrazení. Například voláním metody Html.TextBox() vykreslit textové pole.

Všimněte si, že používáte oddělovače skriptu &lt;% = a %&gt; při volání metody Html.TextBox() i Html.Password() pomocné rutiny. Tyto pomocné rutiny jednoduše vrátí řetězec. Je třeba volat Response.Write() k vykreslení řetězec do prohlížeče.

Pomocí metody HTML Helper je volitelný. Jejich usnadnit vám život snížením HTML a skript, který budete muset napsat. Zobrazení v výpis 5 vykreslí přesný stejného formuláře jako zobrazení v výpis 4 bez použití pomocné rutiny HTML.

**Výpis 5 – \Views\Home\Login.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-vb/samples/sample5.aspx)]

Máte také možnost vytvořit vlastní pomocné rutiny HTML. Například můžete vytvořit GridView() Pomocná metoda, která zobrazuje sadu záznamů databáze automaticky do tabulky HTML. Toto téma jsme prozkoumat v tomto kurzu **vytváření Pomocníci HTML vlastní**.

## <a name="using-view-data-to-pass-data-to-a-view"></a>Pomocí zobrazení dat k předávání dat zobrazení

Zobrazení dat slouží k předávání dat z řadiče zobrazení. Vezměte v úvahu zobrazení dat, jako je balíček, který odeslat pomocí e-mailu. Pomocí tohoto balíčku, musí se poslat všechna data z řadič předána do zobrazení. Například řadič v výpis 6 přidá zprávu zobrazovat data.

**Výpis 6 - ProductController.vb**

[!code-vb[Main](asp-net-mvc-views-overview-vb/samples/sample6.vb)]

Řadič ViewData vlastnost představuje kolekci dvojic název a hodnotu. Metoda Index() v výpis 6, přidá položku do kolekce zobrazení dat s názvem zprávy s hodnotou Hello, World!. Při zobrazení vráceném metodou Index(), data zobrazení jsou automaticky předána do zobrazení.

Zobrazení v výpis 7 načte zprávu z dat zobrazení a vykreslí zprávu, která se v prohlížeči.

**Výpis 7 – \Views\Product\Index.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-vb/samples/sample7.aspx)]

Všimněte si, že zobrazení využívá metody pomocné rutiny HTML Html.Encode() při vykreslování zprávy. Pomocné rutiny HTML Html.Encode() kóduje zvláštní znaky, jako &lt; a &gt; do znaky, které jsou bezpečné pro zobrazení na webové stránce. Vždy, když jste vykreslení obsahu, který uživatel odeslal na web, by měl kódování obsahu zabránit útokům vkládání JavaScript.

(Protože jsme vytvořili zprávu sebe v ProductController, jsme nejsou zobrazeny t skutečně potřebujete ke kódování zprávy. Je však dobré která podporují vždy volat metodu Html.Encode() při zobrazování obsahu načteny z zobrazení dat v rámci zobrazení.)

Ve výpisu 7 vzali jsme výhod předat zprávu jednoduchým řetězcem z řadiče zobrazení dat zobrazení. Zobrazení dat můžete použít taky jiné typy dat, jako jsou kolekce záznamů databáze, z řadiče zobrazení. Například pokud chcete zobrazit obsah databázové tabulky produktů v zobrazení, pak by úspěšně prošel zpracováním kolekci databáze záznamů v zobrazení data.

Máte také možnost předat data silného typu zobrazení z řadiče zobrazení. Toto téma jsme prozkoumat v tomto kurzu **Principy silného typu zobrazení dat a zobrazení**.

## <a name="summary"></a>Souhrn

V tomto kurzu poskytuje stručný úvod do architektury ASP.NET MVC zobrazení, data zobrazení a pomocné rutiny HTML. V první části jste zjistili, jak přidat nové zobrazení do projektu. Jste zjistili, že je nutné přidat zobrazení do správné složky k volání z určitý kontroler. V dalším kroku jsme popsané v tématu pomocné rutiny HTML. Jste se dozvěděli, jak pomocné objekty HTML umožňují snadno vygenerovat standardní obsah HTML. Nakonec jste zjistili, jak chcete využít výhod předat data z řadiče zobrazení dat zobrazení.

>[!div class="step-by-step"]
[Předchozí](passing-data-to-view-master-pages-cs.md)
[další](creating-custom-html-helpers-vb.md)