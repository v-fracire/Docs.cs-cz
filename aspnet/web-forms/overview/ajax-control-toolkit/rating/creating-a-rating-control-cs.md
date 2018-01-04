---
uid: web-forms/overview/ajax-control-toolkit/rating/creating-a-rating-control-cs
title: "Vytvoření ovládacího prvku hodnocení (C#) | Microsoft Docs"
author: wenz
description: "Mnoho webů, z elektronického obchodování do lokalit komunity, nabízí svým uživatelům míra články nebo položek. Většinou je potřeba některé kódování úsilí, ale máme..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 969fb28f-2bff-4fc4-b24a-27f5e2534a37
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/rating/creating-a-rating-control-cs
msc.type: authoredcontent
ms.openlocfilehash: 7c004522ac72b848e42320862d77bced0c11ca15
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2017
---
<a name="creating-a-rating-control-c"></a>Vytvoření ovládacího prvku hodnocení (C#)
====================
podle [Christian Wenz](https://github.com/wenz)

[Stáhněte si kód](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/rating0.cs.zip) nebo [stáhnout PDF](http://download.microsoft.com/download/2/d/c/2dc10e34-6983-41d4-9c08-f78f5387d32b/rating0CS.pdf)

> Mnoho webů, z elektronického obchodování do lokalit komunity, nabízí svým uživatelům míra články nebo položek. Většinou je potřeba některé kódování úsilí, ale musíme Toolkitu naše uvolnění.


## <a name="overview"></a>Přehled

Mnoho webů, z elektronického obchodování do lokalit komunity, nabízí svým uživatelům míra články nebo položek. Většinou je potřeba některé kódování úsilí, ale musíme Toolkitu naše uvolnění.

## <a name="steps"></a>Kroky

Je třeba nejprve všech, (aspoň) dva druhy bitové kopie: jednu pro vyplnění položku hodnocení a jeden pro položku prázdný hodnocení. Položku hodnocení je obvykle hvězdu nebo emotikona. V tomto scénáři zjistíte tři soubory, smiley.png a empty.png a emotikona done.png v rámci stahování zdrojového kódu pro účely tohoto kurzu.

Pak vytvořte nový soubor ASP.NET a začněte přidáte `ScriptManager` řízení k němu:

[!code-aspx[Main](creating-a-rating-control-cs/samples/sample1.aspx)]

Poté, přidejte `Rating` řízení z ovládacího prvku ASP.NET AJAX Toolkit. Následující atributy je nutné nastavit v tomto příkladu:

- `CurrentRating`Počáteční hodnocení, který se má použít
- `MaxRating`maximální hodnocení
- `EmptyStarCssClass`Třída šablon stylů CSS pro použití při hodnocení položky (hvězdička) je prázdný
- `FilledStarCssClass`třídy šablon stylů CSS použité při vyplňování položku hodnocení (hvězdička)
- `StarCssClass`Třída šablon stylů CSS pro viditelné stat
- `WaitingStarCssClass`třídy šablon stylů CSS použité při hodnocení hvězdičkami budou odeslána zpět do serveru

A zde je kód, který vytvoří ovládacího prvku hodnocení s pěti položek (Veselé obličeje), ve kterých žádný vyplní se původně:

[!code-aspx[Main](creating-a-rating-control-cs/samples/sample2.aspx)]

Tři odkazované tříd CSS nyní třeba zobrazit soubory příslušné bitové kopie, což je snadno pomocí šablon stylů CSS:

[!code-css[Main](creating-a-rating-control-cs/samples/sample3.css)]

Ujistěte se, zadejte šířku a výšku tři bitových kopií, v opačném případě bude vypadat zobrazení trochu messed nahoru.

Nakonec výsledek hodnocení by měl být uživateli zobrazí (nebo alespoň uloženy v databázi). Proto přidáte popisek pro výstup zprávu SMS a tlačítko odeslání odeslat zpět hodnocení formuláře k serveru:

[!code-aspx[Main](creating-a-rating-control-cs/samples/sample4.aspx)]

V kódu na straně serveru přístup prostřednictvím ovládacího prvku hodnocení jeho `ID` a pro přístup k jeho `CurrentRating` vlastnost, která je počet položek vybraných hodnocení, v našem příkladu hodnotu v rozmezí 0 až 5.

[!code-aspx[Main](creating-a-rating-control-cs/samples/sample5.aspx)]

Uložit stránky a načíst do prohlížeče. Po přesunutí ukazatele myši položky (původně prázdné) hodnocení, dojde k vliv JavaScript: změny hodnocení. Když kliknete na sadu hvězdiček, se zachová aktuální hodnocení. Nakonec při odesílání formuláře kódu na straně serveru výstupy vybrané hodnocení.


[![Vytváření hodnocení systému s minimálním kódu](creating-a-rating-control-cs/_static/image2.png)](creating-a-rating-control-cs/_static/image1.png)

Vytváření hodnocení systému s minimálním kódu ([Kliknutím zobrazit obrázek v plné velikosti](creating-a-rating-control-cs/_static/image3.png))

>[!div class="step-by-step"]
[Další](creating-a-rating-control-vb.md)