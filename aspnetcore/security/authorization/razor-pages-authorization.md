---
title: "Konvence autorizace stránky Razor v ASP.NET Core"
author: guardrex
description: "Zjistěte, jak řídit přístup k stránky s konvence při spuštění, které autorizace uživatelů a anonymní uživatelům umožní přístup k jednotlivým stránkám nebo složky stránek."
ms.author: riande
manager: wpickett
ms.date: 10/27/2017
ms.topic: article
ms.assetid: f65ad22d-9472-478a-856c-c59c8681fa71
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 36acf3c06a462882972c5f389d544d98cadc35f6
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2017
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a>Konvence autorizace stránky Razor v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

Jedním ze způsobů k řízení přístupu ve vaší aplikaci stránky Razor je použití autorizace konvence při spuštění. Tyto konvence umožňují autorizace uživatelů a anonymní uživatelům umožní přístup k jednotlivým stránkám nebo složky stránek. Platí zásady popsané v tomto tématu automaticky [filtry autorizace](xref:mvc/controllers/filters#authorization-filters) pro řízení přístupu.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/sample) ([stažení](xref:tutorials/index#how-to-download-a-sample))

## <a name="require-authorization-to-access-a-page"></a>Vyžadovat oprávnění pro přístup k stránky

Použití [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) konvence prostřednictvím [AddRazorPagesOptions](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.addrazorpagesoptions) přidat [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) na stránku v zadané cestě:

[!code-csharp[Main](razor-pages-authorization/sample/Startup.cs?name=snippet1&highlight=2,4)]

Zadaná cesta je cesta modul zobrazení, která je relativní cesta stránky Razor kořenové bez rozšíření a obsahující jenom lomítka.

[AuthorizePage přetížení](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage#Microsoft_Extensions_DependencyInjection_PageConventionCollectionExtensions_AuthorizePage_Microsoft_AspNetCore_Mvc_ApplicationModels_PageConventionCollection_System_String_System_String_) je k dispozici, pokud je třeba zadat zásad autorizace.

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Vyžadovat autorizace pro přístup ke složce stránek

Použití [AuthorizeFolder](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizefolder) konvence prostřednictvím [AddRazorPagesOptions](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.addrazorpagesoptions) přidat [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) na všechny stránky ve složce v zadané cestě:

[!code-csharp[Main](razor-pages-authorization/sample/Startup.cs?name=snippet1&highlight=2,5)]

Zadaná cesta je cesta modul zobrazení, která je relativní cesta kořenové stránky Razor.

[AuthorizeFolder přetížení](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizefolder#Microsoft_Extensions_DependencyInjection_PageConventionCollectionExtensions_AuthorizeFolder_Microsoft_AspNetCore_Mvc_ApplicationModels_PageConventionCollection_System_String_System_String_) je k dispozici, pokud je třeba zadat zásad autorizace.

## <a name="allow-anonymous-access-to-a-page"></a>Povolení anonymního přístupu na stránku

Použití [AllowAnonymousToPage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustopage) konvence prostřednictvím [AddRazorPagesOptions](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.addrazorpagesoptions) přidat [AllowAnonymousFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) na stránku v zadané cestě:

[!code-csharp[Main](razor-pages-authorization/sample/Startup.cs?name=snippet1&highlight=2,6)]

Zadaná cesta je cesta modul zobrazení, která je relativní cesta stránky Razor kořenové bez rozšíření a obsahující jenom lomítka.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Povolit anonymní přístup do složky stránek

Použití [AllowAnonymousToFolder](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustofolder) konvence prostřednictvím [AddRazorPagesOptions](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.addrazorpagesoptions) přidat [AllowAnonymousFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) na všechny stránky ve složce v zadané cestě:

[!code-csharp[Main](razor-pages-authorization/sample/Startup.cs?name=snippet1&highlight=2,7)]

Zadaná cesta je cesta modul zobrazení, která je relativní cesta kořenové stránky Razor.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>Poznámka: na kombinování oprávnění a anonymní přístup

Je zcela vhodný k určení, že složku stránek vyžadují autorizace a určit, že na stránce v této složce umožňuje anonymní přístup:

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

Naopak, ale není pravda. Nelze deklarovat složku stránek pro anonymní přístup a určení stránky v rámci pro ověřování:

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private") 
```

Vyžadující ověření na stránce privátní nebude fungovat, protože při jak `AllowAnonymousFilter` a `AuthorizeFilter` na stránce jsou použity filtry `AllowAnonymousFilter` wins a řídí přístup.

## <a name="see-also"></a>Viz také

* [Syntaxe Razor stránky vlastní trasy a stránka zprostředkovatele modelu](xref:mvc/razor-pages/razor-pages-convention-features)
* [PageConventionCollection](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageconventioncollection) – třída