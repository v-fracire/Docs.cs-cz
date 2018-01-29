---
title: "Ukládání do mezipaměti Middleware v ASP.NET Core odpovědi"
author: guardrex
description: "Zjistěte, jak konfigurovat a používat Middleware ukládání do mezipaměti odpovědi v aplikacích ASP.NET Core."
ms.author: riande
manager: wpickett
ms.date: 08/22/2017
ms.topic: article
ms.prod: asp.net-core
uid: performance/caching/middleware
ms.openlocfilehash: f3312d0c333b47169c71891eea79f03be0abcfa3
ms.sourcegitcommit: 216dfac27542f10a79274a9ce60dc449e888ed20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2017
---
# <a name="response-caching-middleware-in-aspnet-core"></a>Ukládání do mezipaměti Middleware v ASP.NET Core odpovědi

Podle [Luke Latham](https://github.com/guardrex) a [Luo Jan](https://github.com/JunTaoLuo)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([stažení](xref:tutorials/index#how-to-download-a-sample))

Tento dokument obsahuje podrobnosti o tom, jak nakonfigurovat Middleware ukládání do mezipaměti odpovědi v aplikacích ASP.NET Core. Middleware Určuje, pokud jsou odpovědi lze uložit do mezipaměti, ukládá odpovědi a slouží odpovědi z mezipaměti. Úvod do ukládání do mezipaměti HTTP a `ResponseCache` atributů najdete v tématu [ukládání odpovědí do mezipaměti](response.md).

## <a name="package"></a>Balíček
Pokud chcete middleware v projektu, přidejte odkaz na [ `Microsoft.AspNetCore.ResponseCaching` ](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) balíček nebo použít [ `Microsoft.AspNetCore.All` ](https://www.nuget.org/packages/Microsoft.AspNetCore.All/) balíčku.

## <a name="configuration"></a>Konfigurace
V `ConfigureServices`, přidat middleware ke kolekci služby.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET základní 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](middleware/samples/2.x/Program.cs?name=snippet1&highlight=4)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET základní 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](middleware/samples/1.x/Startup.cs?name=snippet1&highlight=3)]

---

Nakonfigurovat aplikaci, aby používala middlewaru s `UseResponseCaching` metoda rozšíření, která přidá middleware kanálu zpracování požadavků. Ukázková aplikace přidá [ `Cache-Control` ](https://tools.ietf.org/html/rfc7234#section-5.2) hlavičky odpovědi, který ukládá do mezipaměti odpovědi lze uložit do mezipaměti pro až 10 sekund. Odesílá vzorek [ `Vary` ](https://tools.ietf.org/html/rfc7231#section-7.1.4) záhlaví pro konfiguraci middlewaru k obsluze odpovědi v mezipaměti pouze v případě [ `Accept-Encoding` ](https://tools.ietf.org/html/rfc7231#section-5.3.4) záhlaví následných žádostí se shoduje s původní žádost.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET základní 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](middleware/samples/2.x/Program.cs?name=snippet1&highlight=8)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET základní 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](middleware/samples/1.x/Startup.cs?name=snippet2&highlight=3)]

---

Middleware ukládání do mezipaměti odpovědi pouze ukládá do mezipaměti odpovědi serveru 200 (OK). Žádné jiné reakce, včetně [chybové stránky](xref:fundamentals/error-handling), ignorují middlewarem.

> [!WARNING]
> Odpovědím obsahujícím obsah pro klienty ověřené musí být označen jako není Uložitelný, aby se zabránilo middleware z ukládání a současné obsluhování těchto odpovědí. V tématu [podmínky pro ukládání do mezipaměti](#conditions-for-caching) podrobnosti o tom, jak middleware Určuje, zda odpověď lze uložit do mezipaměti.

## <a name="options"></a>Možnosti
Middleware nabízí tři možnosti pro řízení ukládání do mezipaměti odpovědi.

| Možnost                | Výchozí hodnota |
| --------------------- | ------------- |
| UseCaseSensitivePaths | Určuje, pokud jsou odpovědi ukládat do mezipaměti na malá a velká písmena cesty.</p><p>Výchozí hodnota je `false`. |
| MaximumBodySize       | Největší velikost lze uložit do mezipaměti pro text odpovědi v bajtech.</p>Výchozí hodnota je `64 * 1024 * 1024` (64 MB). |
| SizeLimit             | Omezení velikosti pro middleware mezipaměti odpovědi v bajtech. Výchozí hodnota je `100 * 1024 * 1024` (100 MB). |

Následující příklad konfiguruje middleware do mezipaměti odpovědi menší než nebo rovna 1024 bajtů pomocí cesty malá a velká písmena, ukládání odpovědí na `/page1` a `/Page1` samostatně.

```csharp
services.AddResponseCaching(options =>
{
    options.UseCaseSensitivePaths = true;
    options.MaximumBodySize = 1024;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys
Při použití MVC, `ResponseCache` atribut určuje parametry, které jsou nezbytné pro nastavení odpovídající hlavičky pro ukládání odpovědí do mezipaměti. Parametr pouze `ResponseCache` je atribut, který vyžaduje výhradně middleware `VaryByQueryKeys`, který neodpovídá skutečné záhlaví HTTP. Další informace najdete v tématu [ResponseCache atribut](response.md#responsecache-attribute).

Pokud nepoužíváte MVC, můžete měnit ukládání do mezipaměti odpovědi `VaryByQueryKeys` funkce. Použití `ResponseCachingFeature` přímo z `IFeatureCollection` z `HttpContext`:

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();
if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

## <a name="http-headers-used-by-response-caching-middleware"></a>Hlavičky HTTP používaný Middleware ukládání do mezipaměti odpovědi
Ukládání do mezipaměti podle middleware odpovědi je nakonfigurován pomocí hlavičky protokolu HTTP. Relevantní hlavičky jsou uvedeny pod poznámky na tom, jak ovlivňují ukládání do mezipaměti.

| Záhlaví | Podrobnosti |
| ------ | ------- |
| Autorizace | Odpověď není v mezipaměti, pokud existuje hlavička. |
| Cache-Control | Middleware uvažuje pouze ukládání do mezipaměti odpovědi, které jsou označené jako `public` direktiva mezipaměti. Můžete řídit ukládání do mezipaměti s následujícími parametry:<ul><li>Maximální stáří</li><li>maximální počet zastaralé &#8224;</li><li>čerstvě min.</li><li>musí revalidate</li><li>Ne mezipaměti</li><li>Ne – úložiště</li><li>pouze v případě mezipaměti</li><li>private</li><li>public</li><li>s maxage</li><li>proxy server revalidate &#8225;</li></ul>&#8224; Pokud není zadáno žádné omezení na `max-stale`, middleware neprovede žádnou akci.<br>&#8225; `proxy-revalidate` má stejný účinek jako `must-revalidate`.<br><br>Další informace najdete v tématu [RFC 7231: požadavku direktivy Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2.1). |
| Direktiva pragma | A `Pragma: no-cache` hlavičky v požadavku vytváří stejného efektu jako `Cache-Control: no-cache`. Tuto hlavičku je přepsat relevantní direktivy v `Cache-Control` záhlaví, pokud je k dispozici. Za pro zpětnou kompatibilitu s HTTP/1.0. |
| Set-Cookie | Odpověď není v mezipaměti, pokud existuje hlavička. |
| lišit | `Vary` Záhlaví slouží k odlišení odpověď uložená v mezipaměti jiné záhlaví. Například můžete pomocí kódování zahrnutím mezipaměti odpovědi `Vary: Accept-Encoding` hlavičky, která ukládá do mezipaměti odpovědi pro požadavky s hlavičky `Accept-Encoding: gzip` a `Accept-Encoding: text/plain` samostatně. Odpověď se hodnota hlavičky `*` nikdy neuloží. |
| Vypršení platnosti | Odpovědi, které tuto hlavičku považují za zastaralé není uložen nebo načíst, pokud není přepsána jiná `Cache-Control` hlavičky. |
| If-None-Match | Úplnou odpověď je zpracovat z mezipaměti, pokud hodnota není `*` a `ETag` odpovědi neodpovídá zadanými hodnotami. Odpověď 304 (upraveno), jinak je zpracovat. |
| Pokud upravit – od | Pokud `If-None-Match` hlavičky není přítomen, úplnou odpověď je zpracovat z mezipaměti, pokud je novější než hodnota zadaná data odpovědi v mezipaměti. Odpověď 304 (upraveno), jinak je zpracovat. |
| Datum | Když obsluhující z mezipaměti, `Date` záhlaví je nastavení middleware, pokud nebyl zadán v původní odpovědi. |
| Délka obsahu | Když obsluhující z mezipaměti, `Content-Length` záhlaví je nastavení middleware, pokud nebyl zadán v původní odpovědi. |
| stáří | `Age` Záhlaví odeslaný v odpovědi původní je ignorována. Middleware vypočítá novou hodnotu, pokud obsluhující odpovědi v mezipaměti. |

## <a name="caching-respects-request-cache-control-directives"></a>Ukládání do mezipaměti respektuje direktivy požadavek Cache-Control

Middleware respektuje pravidla [ukládání do mezipaměti HTTP 1.1 specifikace](https://tools.ietf.org/html/rfc7234#section-5.2). Pravidla vyžadovat mezipaměti vyhovět platná `Cache-Control` hlavička odeslaná klientem. V části specifikace, klient provádět požadavky `no-cache` hodnota hlavičky a force serveru pro generování novou odpověď pro každý požadavek. V současné době není žádné vývojáři řídit toto chování ukládání do mezipaměti při použití middleware, protože middleware dodržuje specifikaci oficiální ukládání do mezipaměti.

[Budoucí vylepšení middleware](https://github.com/aspnet/ResponseCaching/issues/96) povolí přístup konfigurace middleware pro ukládání do mezipaměti scénáře kde žádost `Cache-Control` záhlaví třeba ji ignorovat při rozhodování, která bude sloužit odpovědi v mezipaměti. Při hledání větší kontrolu nad chování ukládání do mezipaměti, prozkoumejte jiné funkce ukládání do mezipaměti ASP.NET Core. Najdete v následujících tématech:

* [Ukládání do mezipaměti v paměti](xref:performance/caching/memory)
* [Práce s distribuované mezipaměti](xref:performance/caching/distributed)
* [Značka Pomocník jádro ASP.NET MVC do mezipaměti](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)
* [Pomocník značky distribuované mezipaměti](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)

## <a name="troubleshooting"></a>Poradce při potížích
Není-li chování ukládání do mezipaměti podle očekávání, ověřte, zda jsou odpovědí lze uložit do mezipaměti a je schopný obsluhovány z mezipaměti tak, že prověří hlavičky příchozí žádosti a odpovědi na odchozí. Povolení [protokolování](xref:fundamentals/logging/index) může pomoci při ladění. Middleware protokoly ukládání do mezipaměti chování, pokud je odpověď načten z mezipaměti.

Při testování a řešení potíží s chování ukládání do mezipaměti, prohlížeče může nastavit hlavičky žádosti, které ovlivňují ukládání do nežádoucího způsoby. Například může nastavit prohlížeče `Cache-Control` hlavičky k `no-cache` při aktualizaci stránky. Tyto nástroje můžete explicitně nastavit hlavičky požadavku a jsou upřednostněny testování ukládání do mezipaměti:

* [Fiddler](http://www.telerik.com/fiddler)
* [FireBug](http://getfirebug.com/)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Podmínky pro ukládání do mezipaměti
* Žádost musí mít za následek 200 (OK) odpověď ze serveru.
* Metoda požadavku musí být GET nebo HEAD.
* Terminálu middleware, jako je například Middleware statické soubory, nesmí zpracovat odpověď před Middleware ukládání do mezipaměti odpovědi.
* `Authorization` Nesmí být záhlaví.
* `Cache-Control`Hlavička parametry musí být platný, a odpovědi musí být označen `public` a nesmí být označený `private`.
* `Pragma: no-cache` Nebo hodnota hlavičky se nesmějí vyskytovat Pokud `Cache-Control` hlavičky není přítomný, jako `Cache-Control` záhlaví přepsání `Pragma` záhlaví, pokud jsou k dispozici.
* `Set-Cookie` Nesmí být záhlaví.
* `Vary`Hlavička parametry musí být platný a není rovno `*`.
* `Content-Length` Hodnota hlavičky (Pokud nastavit) musí odpovídat velikost textu odpovědi.
* [IHttpSendFileFeature](/aspnet/core/api/microsoft.aspnetcore.http.features.ihttpsendfilefeature) nepoužívá.
* Odpověď nesmí být zastaralé podle specifikace `Expires` záhlaví a `max-age` a `s-maxage` mezipaměti direktivy.
* Ukládání odpovědi do vyrovnávací paměti je úspěšné, a velikost odpovědi je menší než nakonfigurované nebo výchozí `SizeLimit`.
* Odpovědi musí být lze uložit do mezipaměti podle požadavků [RFC 7234](https://tools.ietf.org/html/rfc7234) specifikace. Například `no-store` – direktiva nesmí existovat v pole hlavičky požadavku nebo odpovědi. V tématu *část 3: uložení odpovědí v mezipaměti* z [RFC 7234](https://tools.ietf.org/html/rfc7234) podrobnosti.

> [!NOTE]
> Antiforgery systému ke generování tokenů zabezpečení, aby se zabránilo webů požadavku padělání (proti útokům CSRF) před útoky nastaví `Cache-Control` a `Pragma` záhlaví `no-cache` tak, aby se neukládají do mezipaměti odpovědi.

## <a name="additional-resources"></a>Další zdroje

* [Spuštění aplikace](xref:fundamentals/startup)
* [Middleware](xref:fundamentals/middleware)
* [Ukládání do mezipaměti v paměti](xref:performance/caching/memory)
* [Práce s distribuované mezipaměti](xref:performance/caching/distributed)
* [Detekovat změny s tokeny změn](xref:fundamentals/primitives/change-tokens)
* [Ukládání odpovědí do mezipaměti](xref:performance/caching/response)
* [Pomocník značky mezipaměti](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)
* [Pomocník značky distribuované mezipaměti](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)