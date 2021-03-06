---
uid: web-forms/overview/ajax-control-toolkit/passwordstrength/testing-the-strength-of-a-password-vb
title: Testování síly hesla (VB) | Dokumentace Microsoftu
author: wenz
description: Hesla jsou skoro kdekoli, vyžaduje, aby opožděné uživatelé mívají sklony zvolte jednoduchá hesla, které se podařilo. PasswordStrength ovládacího prvku ASP. N...
ms.author: riande
ms.date: 06/02/2008
ms.assetid: 9215a37f-3133-4887-8ed2-3689f3a53551
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/passwordstrength/testing-the-strength-of-a-password-vb
msc.type: authoredcontent
ms.openlocfilehash: db4b2a6bbdb0716442b104c03d0c4138bf60f9be
ms.sourcegitcommit: 45ac74e400f9f2b7dbded66297730f6f14a4eb25
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "41753272"
---
<a name="testing-the-strength-of-a-password-vb"></a>Testování síly hesla (VB)
====================
podle [Christian Wenz](https://github.com/wenz)

[Stáhněte si kód](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/PasswordStrength0.vb.zip) nebo [stahovat PDF](http://download.microsoft.com/download/2/d/c/2dc10e34-6983-41d4-9c08-f78f5387d32b/passwordstrength0VB.pdf)

> Hesla jsou skoro kdekoli, vyžaduje, aby opožděné uživatelé mívají sklony zvolte jednoduchá hesla, které se podařilo. PasswordStrength ovládacího prvku ASP.NET AJAX Control Toolkit můžete zkontrolovat, jak kvalitní je heslo.


## <a name="overview"></a>Přehled

Hesla jsou skoro kdekoli, vyžaduje, aby opožděné uživatelé mívají sklony zvolte jednoduchá hesla, které se podařilo. `PasswordStrength` Ovládacího prvku ASP.NET AJAX Control Toolkit můžete zkontrolovat, jak kvalitní je heslo.

## <a name="steps"></a>Kroky

`PasswordStrength` Ovládací prvek textové pole rozšiřuje a zkontroluje, zda je heslo v něm dostatečné. Nabízí celou řadu možností pomocí atributů Tady jsou některé z nich:

- `MinimumNumericCharacters` minimální počet číslic v hesle
- `MinimumSymbolCharacters` minimální počet znaků symbolu (ne písmena a číslice) v hesle
- `PreferredPasswordLength` Minimální délka hesla
- `RequiresUpperAndLowerCaseCharacters` Určuje, zda heslo musí používat velká a malá písmena

`StrengthIndicatorType` Poskytuje informace o představení sílu hesla, jako text (hodnota `"Text"`) nebo jako typ indikátoru průběhu (hodnota `"BarIndicator"`). V `DisplayPosition` atribut, můžete nakonfigurovat ve kterém se zobrazí informace. Tady je úplný příklad, včetně technologie ASP.NET AJAX `ScriptManager` ovládací prvek, `PasswordStrength` ovládacího prvku a samozřejmě textového pole, ve kterém může uživatel zadat heslo. Druhé pole je představu, běžného textového pole a pole hesla tak, aby se zobrazí při vývoji co píšete.

[!code-aspx[Main](testing-the-strength-of-a-password-vb/samples/sample1.aspx)]

Spusťte stránku a zadejte okamžitě: pouze poté, co jste zadali, malá písmena, velká písmena, číslice a symboly, heslo se bude považovat za jako nedělitelné.


[![Nyní je (úplně) dobré heslo](testing-the-strength-of-a-password-vb/_static/image2.png)](testing-the-strength-of-a-password-vb/_static/image1.png)

Nyní je heslo (úplně) dobré ([kliknutím ji zobrazíte obrázek v plné velikosti](testing-the-strength-of-a-password-vb/_static/image3.png))

> [!div class="step-by-step"]
> [Předchozí](testing-the-strength-of-a-password-cs.md)
