---
title: "Ostatní rozhraní API"
author: rick-anderson
description: "Tento dokument popisuje rozhraní ASP.NET Core ochrany dat ISecret."
keywords: ASP.NET Core, ochrany dat, ISecret
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 512c6ba7-88ec-47e4-a656-6b30350b34e6
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: f5d6920f9f229bd480a76c952dab30efb7d9eff5
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2017
---
# <a name="miscellaneous-apis"></a>Ostatní rozhraní API

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> Typy, které implementují některá z následujících rozhraní by měly být vláken pro více volající.

## <a name="isecret"></a>ISecret

`ISecret` Rozhraní představuje tajná hodnota, jako je například materiál kryptografické klíče. Obsahuje následující plochy rozhraní API:

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

`WriteSecretIntoBuffer` Metoda naplní poskytnutá vyrovnávací paměť s nezpracovaná tajná hodnota. Z důvodu toto rozhraní API trvá vyrovnávací paměti jako parametr místo vrácení `byte[]` přímo je, že díky volající možnost připnout objekt vyrovnávací paměti, omezuje jeho vystavení tajný spravované uvolňování paměti.

`Secret` Typ je konkrétní implementaci `ISecret` se uloží tajná hodnota v paměti v procesu. Na platformách systému Windows, jsou zašifrovaná tajná hodnota prostřednictvím [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).