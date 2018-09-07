---
title: Vylepšení aplikace z externího sestavení v ASP.NET Core s IHostingStartup
author: guardrex
description: Zjistěte, jak rozšířit aplikace ASP.NET Core z externího sestavení přes IHostingStartup implementace.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2018
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: 2eddfa03b28564fcca7cc098e353b05e23b7c6f6
ms.sourcegitcommit: a669c4e3f42e387e214a354ac4143555602e6f66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43336231"
---
# <a name="enhance-an-app-from-an-external-assembly-in-aspnet-core-with-ihostingstartup"></a><span data-ttu-id="a3496-103">Vylepšení aplikace z externího sestavení v ASP.NET Core s IHostingStartup</span><span class="sxs-lookup"><span data-stu-id="a3496-103">Enhance an app from an external assembly in ASP.NET Core with IHostingStartup</span></span>

<span data-ttu-id="a3496-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a3496-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a3496-105">[IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup) (který je hostitelem spouštěcí) implementace vylepšení přidá do aplikace při spuštění z externího sestavení.</span><span class="sxs-lookup"><span data-stu-id="a3496-105">An [IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup) (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="a3496-106">Například externí knihovnu můžete hostování implementace spuštění uvést další konfigurace zprostředkovatele nebo služby do aplikace.</span><span class="sxs-lookup"><span data-stu-id="a3496-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span> <span data-ttu-id="a3496-107">`IHostingStartup` *je k dispozici v ASP.NET Core 2.0 nebo novější.*</span><span class="sxs-lookup"><span data-stu-id="a3496-107">`IHostingStartup` *is available in ASP.NET Core 2.0 or later.*</span></span>

<span data-ttu-id="a3496-108">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([stažení](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a3496-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="a3496-109">Atribut HostingStartup</span><span class="sxs-lookup"><span data-stu-id="a3496-109">HostingStartup attribute</span></span>

<span data-ttu-id="a3496-110">A [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) atribut indikuje přítomnost hostování sestavení po spuštění k aktivaci za běhu.</span><span class="sxs-lookup"><span data-stu-id="a3496-110">A [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="a3496-111">Vstupní sestavení nebo sestavení obsahující `Startup` třídy automaticky vyhledávat `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="a3496-111">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="a3496-112">Seznam sestavení, které chcete hledat `HostingStartup` atributy je načtená v době běhu z konfigurace v [WebHostDefaults.HostingStartupAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupassemblieskey).</span><span class="sxs-lookup"><span data-stu-id="a3496-112">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupassemblieskey).</span></span> <span data-ttu-id="a3496-113">Načíst seznam sestavení, které chcete vyloučit ze zjišťování z [WebHostDefaults.HostingStartupExcludeAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupexcludeassemblieskey).</span><span class="sxs-lookup"><span data-stu-id="a3496-113">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupexcludeassemblieskey).</span></span> <span data-ttu-id="a3496-114">Další informace najdete v tématu [webového hostitele: hostování při spuštění sestavení](xref:fundamentals/host/web-host#hosting-startup-assemblies) a [webového hostitele: sestavení vyloučit při spuštění hostování](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="a3496-114">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="a3496-115">V následujícím příkladu, obor názvů hostování sestavení po spuštění je `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="a3496-115">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="a3496-116">Třída obsahující kód hostování spuštění je `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="a3496-116">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="a3496-117">`HostingStartup` Atribut se obvykle nachází v hostitelském sestavení po spuštění `IHostingStartup` souboru implementace třídy.</span><span class="sxs-lookup"><span data-stu-id="a3496-117">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="a3496-118">Zjistit načtená hostingu při spuštění sestavení</span><span class="sxs-lookup"><span data-stu-id="a3496-118">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="a3496-119">Ke zřízení načtená hostingu při spuštění sestavení, povolte protokolování a v protokolech aplikace.</span><span class="sxs-lookup"><span data-stu-id="a3496-119">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="a3496-120">Jsou zaznamenány chyby, ke kterým dochází při načítání sestavení.</span><span class="sxs-lookup"><span data-stu-id="a3496-120">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="a3496-121">Načtená hostingu při spuštění sestavení jsou zaznamenány na úrovni ladění a jsou zaznamenány všechny chyby.</span><span class="sxs-lookup"><span data-stu-id="a3496-121">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="a3496-122">Vypnout automatické načítání hostování při spuštění sestavení</span><span class="sxs-lookup"><span data-stu-id="a3496-122">Disable automatic loading of hosting startup assemblies</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="a3496-123">Vypnout automatické načítání hostování při spuštění sestavení, použijte jednu z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="a3496-123">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="a3496-124">Abyste zabránili všechna sestavení po spuštění hostování načítání, nastavte jednu z následujících způsobů `true` nebo `1`:</span><span class="sxs-lookup"><span data-stu-id="a3496-124">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="a3496-125">[Zabránit spuštění hostování](xref:fundamentals/host/web-host#prevent-hosting-startup) hostovat nastavení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a3496-125">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="a3496-126">`ASPNETCORE_PREVENTHOSTINGSTARTUP` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="a3496-126">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="a3496-127">Pokud chcete zabránit konkrétní hostingu při spuštění sestavení z načítání, nastavte na řetězec oddělený středníkem hostování při spuštění sestavení mají vyloučit při spuštění jednu z následujících:</span><span class="sxs-lookup"><span data-stu-id="a3496-127">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="a3496-128">[Hostování sestavení vyloučit při spuštění](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) hostovat nastavení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a3496-128">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="a3496-129">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="a3496-129">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="a3496-130">Vypnout automatické načítání hostování při spuštění sestavení, nastavte jednu z následujících způsobů `true` nebo `1`:</span><span class="sxs-lookup"><span data-stu-id="a3496-130">To disable automatic loading of hosting startup assemblies, set one of the following to `true` or `1`:</span></span>

* <span data-ttu-id="a3496-131">[Zabránit spuštění hostování](xref:fundamentals/host/web-host#prevent-hosting-startup) hostovat nastavení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a3496-131">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
* <span data-ttu-id="a3496-132">`ASPNETCORE_PREVENTHOSTINGSTARTUP` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="a3496-132">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

::: moniker-end

<span data-ttu-id="a3496-133">Pokud nastavení konfigurace hostitele a proměnné prostředí jsou nastaveny, nastavení hostitele řídí chování.</span><span class="sxs-lookup"><span data-stu-id="a3496-133">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="a3496-134">Zakázání hostingu při spuštění sestavení pomocí proměnná hostitele nastavení nebo prostředí globálně zakáže sestavení a může zakázat několik vlastností aplikace.</span><span class="sxs-lookup"><span data-stu-id="a3496-134">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="a3496-135">Projekt</span><span class="sxs-lookup"><span data-stu-id="a3496-135">Project</span></span>

<span data-ttu-id="a3496-136">Vytvoření hostitelského spouštěcího s jedním z následujících typů projektu:</span><span class="sxs-lookup"><span data-stu-id="a3496-136">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="a3496-137">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="a3496-137">Class library</span></span>](#class-library)
* [<span data-ttu-id="a3496-138">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="a3496-138">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="a3496-139">Knihovna tříd</span><span class="sxs-lookup"><span data-stu-id="a3496-139">Class library</span></span>

<span data-ttu-id="a3496-140">Hostování rozšíření spuštění lze zadat v knihovně tříd.</span><span class="sxs-lookup"><span data-stu-id="a3496-140">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="a3496-141">Knihovna obsahuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="a3496-141">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="a3496-142">[Ukázkový kód](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) zahrnuje aplikace stránky Razor, *HostingStartupApp*a knihovny tříd, *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="a3496-142">The [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="a3496-143">Knihovna tříd:</span><span class="sxs-lookup"><span data-stu-id="a3496-143">The class library:</span></span>

* <span data-ttu-id="a3496-144">Obsahuje třídu spuštění hostování `ServiceKeyInjection`, která implementuje `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="a3496-144">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="a3496-145">`ServiceKeyInjection` Přidá dvojici řetězce služby pro konfiguraci aplikace pomocí zprostředkovatele konfigurace v paměti ([AddInMemoryCollection](/dotnet/api/microsoft.extensions.configuration.memoryconfigurationbuilderextensions.addinmemorycollection)).</span><span class="sxs-lookup"><span data-stu-id="a3496-145">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](/dotnet/api/microsoft.extensions.configuration.memoryconfigurationbuilderextensions.addinmemorycollection)).</span></span>
* <span data-ttu-id="a3496-146">Zahrnuje `HostingStartup` atribut, který identifikuje obor názvů a třídy spuštění hostování.</span><span class="sxs-lookup"><span data-stu-id="a3496-146">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="a3496-147">`ServiceKeyInjection` Třídy [konfigurovat](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) metoda používá [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) přidání vylepšení do aplikace.</span><span class="sxs-lookup"><span data-stu-id="a3496-147">The `ServiceKeyInjection` class's [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) method uses an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) to add enhancements to an app.</span></span> <span data-ttu-id="a3496-148">`IHostingStartup.Configure` v hostitelských spuštění sestavení je volána modulem runtime před `Startup.Configure` v uživatelském kódu, který umožňuje přepsat žádnou konfiguraci poskytované hostujícím sestavení při spuštění uživatelského kódu.</span><span class="sxs-lookup"><span data-stu-id="a3496-148">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

<span data-ttu-id="a3496-149">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="a3496-149">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="a3496-150">Aplikace indexovou stránku přečte a vykreslí konfigurační hodnoty pro dva klíče, nastavit podle knihovny tříd hostingu při spuštění sestavení:</span><span class="sxs-lookup"><span data-stu-id="a3496-150">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="a3496-151">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="a3496-151">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="a3496-152">[Ukázkový kód](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) také zahrnuje projektu balíček NuGet, který poskytuje samostatné hostitelské spuštění *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="a3496-152">The [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="a3496-153">Balíček má stejné charakteristiky knihovna tříd je popsáno výše.</span><span class="sxs-lookup"><span data-stu-id="a3496-153">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="a3496-154">Balíček:</span><span class="sxs-lookup"><span data-stu-id="a3496-154">The package:</span></span>

* <span data-ttu-id="a3496-155">Obsahuje třídu spuštění hostování `ServiceKeyInjection`, která implementuje `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="a3496-155">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="a3496-156">`ServiceKeyInjection` Přidá dvojici řetězce služby pro konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="a3496-156">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="a3496-157">Zahrnuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="a3496-157">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="a3496-158">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="a3496-158">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="a3496-159">Aplikace indexovou stránku přečte a vykreslí konfigurační hodnoty pro dva klíče nastavil hostování sestavení po spuštění balíčku:</span><span class="sxs-lookup"><span data-stu-id="a3496-159">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="a3496-160">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="a3496-160">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="a3496-161">Konzolová aplikace bez vstupního bodu</span><span class="sxs-lookup"><span data-stu-id="a3496-161">Console app without an entry point</span></span>

<span data-ttu-id="a3496-162">*Tento přístup je dostupná jenom pro aplikace .NET Core, není rozhraní .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="a3496-162">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="a3496-163">Dynamické vylepšení spuštění hostování, který nevyžaduje odkaz kompilace pro aktivaci lze zadat v konzolové aplikaci bez vstupního bodu.</span><span class="sxs-lookup"><span data-stu-id="a3496-163">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point.</span></span> <span data-ttu-id="a3496-164">Aplikace obsahuje `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="a3496-164">The app contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="a3496-165">Chcete-li vytvořit dynamické hostování spuštění:</span><span class="sxs-lookup"><span data-stu-id="a3496-165">To create a dynamic hosting startup:</span></span>

1. <span data-ttu-id="a3496-166">K implementaci knihovně je vytvořený z třídy, která obsahuje `IHostingStartup` implementace.</span><span class="sxs-lookup"><span data-stu-id="a3496-166">An implementation library is created from the class that contains the `IHostingStartup` implementation.</span></span> <span data-ttu-id="a3496-167">Implementace knihovny je považován za normálního balíčku.</span><span class="sxs-lookup"><span data-stu-id="a3496-167">The implementation library is treated as a normal package.</span></span>
1. <span data-ttu-id="a3496-168">Konzolová aplikace bez vstupního bodu odkazuje balíček knihovny implementace.</span><span class="sxs-lookup"><span data-stu-id="a3496-168">A console app without an entry point references the implementation library package.</span></span> <span data-ttu-id="a3496-169">Protože se používá konzolovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="a3496-169">A console app is used because:</span></span>
   * <span data-ttu-id="a3496-170">Souboru závislostí je k assetu spustitelné aplikace, takže knihovnu nemůžete poskytnout soubor závislosti.</span><span class="sxs-lookup"><span data-stu-id="a3496-170">A dependencies file is a runnable app asset, so a library can't furnish a dependencies file.</span></span>
   * <span data-ttu-id="a3496-171">Knihovny nelze přímo přidat [úložiště balíčků modulu runtime](/dotnet/core/deploying/runtime-store), což vyžaduje spustitelný projekt, který cílí na sdílený modul runtime.</span><span class="sxs-lookup"><span data-stu-id="a3496-171">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>
1. <span data-ttu-id="a3496-172">Publikování aplikace konzoly získat závislosti spuštění hostování.</span><span class="sxs-lookup"><span data-stu-id="a3496-172">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="a3496-173">V důsledku publikování aplikace konzoly je, že se ze souboru závislosti oříznut nepoužité závislé součásti.</span><span class="sxs-lookup"><span data-stu-id="a3496-173">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
1. <span data-ttu-id="a3496-174">Aplikace a jeho závislostí souboru se umístí do úložiště balíčků modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="a3496-174">The app and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="a3496-175">Ke zjištění hostingu při spuštění sestavení a jeho závislosti soubor, budete odkazovat v pár proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="a3496-175">To discover the hosting startup assembly and its dependencies file, they're referenced in a pair of environment variables.</span></span>

<span data-ttu-id="a3496-176">Odkazy na aplikace konzoly [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) balíčku:</span><span class="sxs-lookup"><span data-stu-id="a3496-176">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="a3496-177">A [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) atribut určuje třídu jako implementace `IHostingStartup` pro spuštění při vytváření a načítání [IWebHost](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost).</span><span class="sxs-lookup"><span data-stu-id="a3496-177">A [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the [IWebHost](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost).</span></span> <span data-ttu-id="a3496-178">V následujícím příkladu je obor názvů `StartupEnhancement`, a je třída `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="a3496-178">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="a3496-179">Třída implementuje `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="a3496-179">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="a3496-180">Třídy [konfigurovat](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) metoda používá [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) přidání vylepšení do aplikace.</span><span class="sxs-lookup"><span data-stu-id="a3496-180">The class's [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) method uses an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) to add enhancements to an app.</span></span> <span data-ttu-id="a3496-181">`IHostingStartup.Configure` v hostitelských spuštění sestavení je volána modulem runtime před `Startup.Configure` v uživatelském kódu, který umožňuje přepsat žádnou konfiguraci poskytované hostujícím sestavení při spuštění uživatelského kódu.</span><span class="sxs-lookup"><span data-stu-id="a3496-181">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="a3496-182">Při vytváření `IHostingStartup` závislostí souboru projektu (*\*. deps.json*) nastaví `runtime` umístění sestavení *bin* složky:</span><span class="sxs-lookup"><span data-stu-id="a3496-182">When building an `IHostingStartup` project, the dependencies file (*\*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="a3496-183">Je zobrazena pouze část souboru.</span><span class="sxs-lookup"><span data-stu-id="a3496-183">Only part of the file is shown.</span></span> <span data-ttu-id="a3496-184">Název sestavení v příkladu je `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="a3496-184">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="a3496-185">Zadejte hostitelské sestavení po spuštění</span><span class="sxs-lookup"><span data-stu-id="a3496-185">Specify the hosting startup assembly</span></span>

<span data-ttu-id="a3496-186">Pro třídy knihovny - nebo konzoly aplikace poskytované hostitelem spuštění, zadejte název hostingu při spuštění sestavení `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="a3496-186">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="a3496-187">Proměnná prostředí je středníkem oddělený seznam sestavení.</span><span class="sxs-lookup"><span data-stu-id="a3496-187">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="a3496-188">Vyhledávají pouze na hostitelských při spuštění sestavení `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="a3496-188">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="a3496-189">Ukázkové aplikace *HostingStartupApp*, ke zjištění hostování startupy je popsáno výše, je proměnná prostředí je nastavena na následující hodnotu:</span><span class="sxs-lookup"><span data-stu-id="a3496-189">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="a3496-190">Hostování sestavení po spuštění můžete nastavit také pomocí [hostování při spuštění sestavení](xref:fundamentals/host/web-host#hosting-startup-assemblies) hostovat nastavení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a3496-190">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="a3496-191">Při hostování více spuštění sestaví jsou k dispozici, jejich [konfigurovat](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) metody jsou provedeny v pořadí, že sestavení jsou uvedeny.</span><span class="sxs-lookup"><span data-stu-id="a3496-191">When multiple hosting startup assembles are present, their [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="a3496-192">Aktivace</span><span class="sxs-lookup"><span data-stu-id="a3496-192">Activation</span></span>

<span data-ttu-id="a3496-193">Možnosti pro hostování aktivace po spuštění jsou:</span><span class="sxs-lookup"><span data-stu-id="a3496-193">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="a3496-194">[Úložiště modulu runtime](#runtime-store) &ndash; aktivace nevyžaduje, aby kompilace odkaz pro aktivaci.</span><span class="sxs-lookup"><span data-stu-id="a3496-194">[Runtime store](#runtime-store) &ndash; Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="a3496-195">Ukázková aplikace umístí hostování sestavení po spuštění a soubory závislosti do složky, *nasazení*, které usnadní nasazování hostitelských při spuštění v prostředí multimachine.</span><span class="sxs-lookup"><span data-stu-id="a3496-195">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="a3496-196">*Nasazení* složka obsahuje také skript Powershellu, který vytvoří nebo změní proměnné prostředí pro nasazení systému k povolení spuštění hostování.</span><span class="sxs-lookup"><span data-stu-id="a3496-196">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="a3496-197">Vyžadováno pro aktivaci odkazu za kompilace</span><span class="sxs-lookup"><span data-stu-id="a3496-197">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="a3496-198">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="a3496-198">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="a3496-199">Složky bin projektu</span><span class="sxs-lookup"><span data-stu-id="a3496-199">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="a3496-200">Úložiště modulu runtime</span><span class="sxs-lookup"><span data-stu-id="a3496-200">Runtime store</span></span>

<span data-ttu-id="a3496-201">Hostování implementace spuštění je umístěn v [úložiště modulu runtime](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="a3496-201">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="a3496-202">Kompilace odkaz na sestavení není vyžadováno vylepšené aplikace.</span><span class="sxs-lookup"><span data-stu-id="a3496-202">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="a3496-203">Po spuštění hostování sestavení, hostování počáteční soubor projektu slouží jako soubor manifestu pro [dotnet Restore](/dotnet/core/tools/dotnet-store) příkazu.</span><span class="sxs-lookup"><span data-stu-id="a3496-203">After the hosting startup is built, the hosting startup's project file serves as the manifest file for the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```console
dotnet store --manifest <PROJECT_FILE> --runtime <RUNTIME_IDENTIFIER>
```

<span data-ttu-id="a3496-204">Tímto příkazem umístíte hostování sestavení po spuštění a další závislosti, které nejsou součástí sdílené architektuře v modulu runtime úložišti profilu uživatele na:</span><span class="sxs-lookup"><span data-stu-id="a3496-204">This command places the hosting startup assembly and other dependencies that aren't part of the shared framework in the user profile's runtime store at:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="a3496-205">Windows</span><span class="sxs-lookup"><span data-stu-id="a3496-205">Windows</span></span>](#tab/windows)

```
%USERPROFILE%\.dotnet\store\x64\<TARGET_FRAMEWORK_MONIKER>\<ENHANCEMENT_ASSEMBLY_NAME>\<ENHANCEMENT_VERSION>\lib\<TARGET_FRAMEWORK_MONIKER>\
```

# <a name="macostabmacos"></a>[<span data-ttu-id="a3496-206">macOS</span><span class="sxs-lookup"><span data-stu-id="a3496-206">macOS</span></span>](#tab/macos)

```
/Users/<USER>/.dotnet/store/x64/<TARGET_FRAMEWORK_MONIKER>/<ENHANCEMENT_ASSEMBLY_NAME>/<ENHANCEMENT_VERSION>/lib/<TARGET_FRAMEWORK_MONIKER>/
```

# <a name="linuxtablinux"></a>[<span data-ttu-id="a3496-207">Linux</span><span class="sxs-lookup"><span data-stu-id="a3496-207">Linux</span></span>](#tab/linux)

```
/Users/<USER>/.dotnet/store/x64/<TARGET_FRAMEWORK_MONIKER>/<ENHANCEMENT_ASSEMBLY_NAME>/<ENHANCEMENT_VERSION>/lib/<TARGET_FRAMEWORK_MONIKER>/
```

---

<span data-ttu-id="a3496-208">Pokud vyžadujete k umístění sestavení a závislosti pro globální použití, přidejte `-o|--output` umožňuje `dotnet store` příkaz s následující cestou:</span><span class="sxs-lookup"><span data-stu-id="a3496-208">If you desire to place the assembly and dependencies for global use, add the `-o|--output` option to the `dotnet store` command with the following path:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="a3496-209">Windows</span><span class="sxs-lookup"><span data-stu-id="a3496-209">Windows</span></span>](#tab/windows)

```
%PROGRAMFILES%\dotnet\store\x64\<TARGET_FRAMEWORK_MONIKER>\<ENHANCEMENT_ASSEMBLY_NAME>\<ENHANCEMENT_VERSION>\lib\<TARGET_FRAMEWORK_MONIKER>\
```

# <a name="macostabmacos"></a>[<span data-ttu-id="a3496-210">macOS</span><span class="sxs-lookup"><span data-stu-id="a3496-210">macOS</span></span>](#tab/macos)

```
/usr/local/share/dotnet/store/x64/<TARGET_FRAMEWORK_MONIKER>/<ENHANCEMENT_ASSEMBLY_NAME>/<ENHANCEMENT_VERSION>/lib/<TARGET_FRAMEWORK_MONIKER>/
```

# <a name="linuxtablinux"></a>[<span data-ttu-id="a3496-211">Linux</span><span class="sxs-lookup"><span data-stu-id="a3496-211">Linux</span></span>](#tab/linux)

```
/usr/local/share/dotnet/store/x64/<TARGET_FRAMEWORK_MONIKER>/<ENHANCEMENT_ASSEMBLY_NAME>/<ENHANCEMENT_VERSION>/lib/<TARGET_FRAMEWORK_MONIKER>/
```

---

<span data-ttu-id="a3496-212">**Upravit a umístěte soubor závislosti spuštění hostování**</span><span class="sxs-lookup"><span data-stu-id="a3496-212">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="a3496-213">Modul runtime umístění je určeno v  *\*. deps.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="a3496-213">The runtime location is specified in the *\*.deps.json* file.</span></span> <span data-ttu-id="a3496-214">K aktivaci vylepšení, `runtime` element musíte zadat umístění sestavení rozšíření modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="a3496-214">To activate the enhancement, the `runtime` element must specify the location of the enhancement's runtime assembly.</span></span> <span data-ttu-id="a3496-215">Předpona `runtime` umístění s `lib/<TARGET_FRAMEWORK_MONIKER>/`:</span><span class="sxs-lookup"><span data-stu-id="a3496-215">Prefix the `runtime` location with `lib/<TARGET_FRAMEWORK_MONIKER>/`:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement2.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="a3496-216">Ve vzorovém kódu (*StartupDiagnostics* projektu), úpravu  *\*. deps.json* souboru se provádí pomocí [Powershellu](/powershell/scripting/powershell-scripting) skriptu.</span><span class="sxs-lookup"><span data-stu-id="a3496-216">In the sample code (*StartupDiagnostics* project), modification of the *\*.deps.json* file is performed by a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span> <span data-ttu-id="a3496-217">Skript prostředí PowerShell se automaticky aktivuje cíl sestavení v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="a3496-217">The PowerShell script is automatically triggered by a build target in the project file.</span></span>

<span data-ttu-id="a3496-218">Implementaci  *\*. deps.json* soubor musí být na dostupném místě.</span><span class="sxs-lookup"><span data-stu-id="a3496-218">The implementation's *\*.deps.json* file must be in an accessible location.</span></span>

<span data-ttu-id="a3496-219">Pro použití na uživatele, umístěte ho do *additonalDeps* složku profilu uživatele `.dotnet` nastavení:</span><span class="sxs-lookup"><span data-stu-id="a3496-219">For per-user use, place the file in the *additonalDeps* folder of the user profile's `.dotnet` settings:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="a3496-220">Windows</span><span class="sxs-lookup"><span data-stu-id="a3496-220">Windows</span></span>](#tab/windows)

```
%USERPROFILE%\.dotnet\x64\additionalDeps\<ENHANCEMENT_ASSEMBLY_NAME>\shared\Microsoft.NETCore.App\<SHARED_FRAMEWORK_VERSION>\
```

# <a name="macostabmacos"></a>[<span data-ttu-id="a3496-221">macOS</span><span class="sxs-lookup"><span data-stu-id="a3496-221">macOS</span></span>](#tab/macos)

```
/Users/<USER>/.dotnet/x64/additionalDeps/<ENHANCEMENT_ASSEMBLY_NAME>/shared/Microsoft.NETCore.App/<SHARED_FRAMEWORK_VERSION>/
```

# <a name="linuxtablinux"></a>[<span data-ttu-id="a3496-222">Linux</span><span class="sxs-lookup"><span data-stu-id="a3496-222">Linux</span></span>](#tab/linux)

```
/Users/<USER>/.dotnet/x64/additionalDeps/<ENHANCEMENT_ASSEMBLY_NAME>/shared/Microsoft.NETCore.App/<SHARED_FRAMEWORK_VERSION>/
```

---

<span data-ttu-id="a3496-223">Globální použití, umístěte ho do *additonalDeps* složce instalace .NET Core:</span><span class="sxs-lookup"><span data-stu-id="a3496-223">For global use, place the file in the *additonalDeps* folder of the .NET Core installation:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="a3496-224">Windows</span><span class="sxs-lookup"><span data-stu-id="a3496-224">Windows</span></span>](#tab/windows)

```
%PROGRAMFILES%\dotnet\additionalDeps\<ENHANCEMENT_ASSEMBLY_NAME>\shared\Microsoft.NETCore.App\<SHARED_FRAMEWORK_VERSION>\
```

# <a name="macostabmacos"></a>[<span data-ttu-id="a3496-225">macOS</span><span class="sxs-lookup"><span data-stu-id="a3496-225">macOS</span></span>](#tab/macos)

```
/usr/local/share/dotnet/additionalDeps/<ENHANCEMENT_ASSEMBLY_NAME>/shared/Microsoft.NETCore.App/<SHARED_FRAMEWORK_VERSION>/
```

# <a name="linuxtablinux"></a>[<span data-ttu-id="a3496-226">Linux</span><span class="sxs-lookup"><span data-stu-id="a3496-226">Linux</span></span>](#tab/linux)

```
/usr/local/share/dotnet/additionalDeps/<ENHANCEMENT_ASSEMBLY_NAME>/shared/Microsoft.NETCore.App/<SHARED_FRAMEWORK_VERSION>/
```

---

<span data-ttu-id="a3496-227">Sdílené architektuře verze odpovídá verzi modulu sdíleného modulu runtime, který používá cílové aplikace.</span><span class="sxs-lookup"><span data-stu-id="a3496-227">The shared framework version reflects the version of the shared runtime that the target app uses.</span></span> <span data-ttu-id="a3496-228">Sdílený modul runtime se zobrazí v  *\*. runtimeconfig.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="a3496-228">The shared runtime is shown in the *\*.runtimeconfig.json* file.</span></span> <span data-ttu-id="a3496-229">V ukázkové aplikaci (*HostingStartupApp*), sdílený modul runtime je zadán v *HostingStartupApp.runtimeconfig.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="a3496-229">In the sample app (*HostingStartupApp*), the shared runtime is specified in the *HostingStartupApp.runtimeconfig.json* file.</span></span>

<span data-ttu-id="a3496-230">**Seznam spuštění hostování závislostí souboru**</span><span class="sxs-lookup"><span data-stu-id="a3496-230">**List the hosting startup's dependencies file**</span></span>

<span data-ttu-id="a3496-231">Umístění implementace  *\*. deps.json* souboru je uveden v `DOTNET_ADDITIONAL_DEPS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="a3496-231">The location of the implementation's *\*.deps.json* file is listed in the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="a3496-232">Pokud je soubor umístěn v profilu uživatele *.dotnet* složky, nastavte hodnotu proměnné prostředí:</span><span class="sxs-lookup"><span data-stu-id="a3496-232">If the file is placed in the user profile's *.dotnet* folder, set the environment variable's value to:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="a3496-233">Windows</span><span class="sxs-lookup"><span data-stu-id="a3496-233">Windows</span></span>](#tab/windows)

```
%USERPROFILE%\.dotnet\x64\additionalDeps\
```

# <a name="macostabmacos"></a>[<span data-ttu-id="a3496-234">macOS</span><span class="sxs-lookup"><span data-stu-id="a3496-234">macOS</span></span>](#tab/macos)

```
/Users/<USER>/.dotnet/x64/additionalDeps/
```

# <a name="linuxtablinux"></a>[<span data-ttu-id="a3496-235">Linux</span><span class="sxs-lookup"><span data-stu-id="a3496-235">Linux</span></span>](#tab/linux)

```
/Users/<USER>/.dotnet/x64/additionalDeps/
```

---

<span data-ttu-id="a3496-236">Pokud se soubor nachází v instalaci .NET Core pro globální použití, zadejte úplnou cestu k souboru:</span><span class="sxs-lookup"><span data-stu-id="a3496-236">If the file is placed in the .NET Core installation for global use, provide the full path to the file:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="a3496-237">Windows</span><span class="sxs-lookup"><span data-stu-id="a3496-237">Windows</span></span>](#tab/windows)

```
%PROGRAMFILES%\dotnet\additionalDeps\<ENHANCEMENT_ASSEMBLY_NAME>\shared\Microsoft.NETCore.App\<SHARED_FRAMEWORK_VERSION>\<ENHANCEMENT_ASSEMBLY_NAME>.deps.json
```

# <a name="macostabmacos"></a>[<span data-ttu-id="a3496-238">macOS</span><span class="sxs-lookup"><span data-stu-id="a3496-238">macOS</span></span>](#tab/macos)

```
/usr/local/share/dotnet/additionalDeps/<ENHANCEMENT_ASSEMBLY_NAME>/shared/Microsoft.NETCore.App/<SHARED_FRAMEWORK_VERSION>/<ENHANCEMENT_ASSEMBLY_NAME>.deps.json
```

# <a name="linuxtablinux"></a>[<span data-ttu-id="a3496-239">Linux</span><span class="sxs-lookup"><span data-stu-id="a3496-239">Linux</span></span>](#tab/linux)

```
/usr/local/share/dotnet/additionalDeps/<ENHANCEMENT_ASSEMBLY_NAME>/shared/Microsoft.NETCore.App/<SHARED_FRAMEWORK_VERSION>/<ENHANCEMENT_ASSEMBLY_NAME>.deps.json
```

---

<span data-ttu-id="a3496-240">Pro ukázkovou aplikaci (*HostingStartupApp*) k vyhledání souboru závislosti (*HostingStartupApp.runtimeconfig.json*), souboru závislostí je umístěn v profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="a3496-240">For the sample app (*HostingStartupApp*) to find the dependencies file (*HostingStartupApp.runtimeconfig.json*), the dependencies file is placed in the user's profile.</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="a3496-241">Windows</span><span class="sxs-lookup"><span data-stu-id="a3496-241">Windows</span></span>](#tab/windows)

<span data-ttu-id="a3496-242">Nastavte `DOTNET_ADDITIONAL_DEPS` proměnné prostředí na následující hodnotu:</span><span class="sxs-lookup"><span data-stu-id="a3496-242">Set the `DOTNET_ADDITIONAL_DEPS` environment variable to the following value:</span></span>

```
%UserProfile%\.dotnet\x64\additionalDeps\StartupDiagnostics\
```

# <a name="macostabmacos"></a>[<span data-ttu-id="a3496-243">macOS</span><span class="sxs-lookup"><span data-stu-id="a3496-243">macOS</span></span>](#tab/macos)

<span data-ttu-id="a3496-244">Nastavte `DOTNET_ADDITIONAL_DEPS` proměnné prostředí na následující hodnotu:</span><span class="sxs-lookup"><span data-stu-id="a3496-244">Set the `DOTNET_ADDITIONAL_DEPS` environment variable to the following value:</span></span>

```
/Users/<USER>/.dotnet/x64/additionalDeps/StartupDiagnostics/
```

# <a name="linuxtablinux"></a>[<span data-ttu-id="a3496-245">Linux</span><span class="sxs-lookup"><span data-stu-id="a3496-245">Linux</span></span>](#tab/linux)

<span data-ttu-id="a3496-246">Nastavte `DOTNET_ADDITIONAL_DEPS` proměnné prostředí na následující hodnotu:</span><span class="sxs-lookup"><span data-stu-id="a3496-246">Set the `DOTNET_ADDITIONAL_DEPS` environment variable to the following value:</span></span>

```
/Users/<USER>/.dotnet/x64/additionalDeps/StartupDiagnostics/
```

---

<span data-ttu-id="a3496-247">Příklady toho, jak nastavit proměnné prostředí pro různé operační systémy, najdete v článku [používání více prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="a3496-247">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="a3496-248">**Nasazení**</span><span class="sxs-lookup"><span data-stu-id="a3496-248">**Deployment**</span></span>

<span data-ttu-id="a3496-249">Pro usnadnění nasazení hostování při spuštění v prostředí multimachine, vytvoří ukázkovou aplikaci *nasazení* složky v publikované výstup, který obsahuje:</span><span class="sxs-lookup"><span data-stu-id="a3496-249">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="a3496-250">Hostování při spuštění sestavení.</span><span class="sxs-lookup"><span data-stu-id="a3496-250">The hosting startup assembly.</span></span>
* <span data-ttu-id="a3496-251">Hostování souboru závislostí při spuštění.</span><span class="sxs-lookup"><span data-stu-id="a3496-251">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="a3496-252">Skript prostředí PowerShell, který vytvoří nebo změní `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` a `DOTNET_ADDITIONAL_DEPS` kvůli podpoře aktivace spuštění hostování.</span><span class="sxs-lookup"><span data-stu-id="a3496-252">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="a3496-253">Spusťte skript z příkazového řádku pro správu prostředí PowerShell v systému pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="a3496-253">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="a3496-254">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="a3496-254">NuGet package</span></span>

<span data-ttu-id="a3496-255">Hostování rozšíření spuštění lze zadat balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="a3496-255">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="a3496-256">Balíček nemá `HostingStartup` atribut.</span><span class="sxs-lookup"><span data-stu-id="a3496-256">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="a3496-257">Hostování typům spouštění poskytovaný balíček jsou k dispozici do aplikace pomocí jedné z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="a3496-257">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="a3496-258">Soubor projektu vylepšené aplikace je odkaz na balíček pro hostování při spuštění v souboru projektu vaší aplikace (kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="a3496-258">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="a3496-259">Kompilace odkazem na místě, hostování při spuštění sestavení a všechny jeho závislosti jsou začleněny do závislostí souboru aplikace (*\*. deps.json*).</span><span class="sxs-lookup"><span data-stu-id="a3496-259">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*\*.deps.json*).</span></span> <span data-ttu-id="a3496-260">Vztahuje se na hostování balíček po spuštění sestavení publikovaná [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="a3496-260">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="a3496-261">Hostování spouštěcí závislosti soubor je k dispozici do vylepšené aplikace, jak je popsáno v [úložiště modulu Runtime](#runtime-store) část (bez kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="a3496-261">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="a3496-262">Další informace o balíčcích NuGet a úložiště modulu runtime naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="a3496-262">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="a3496-263">Vytvoření balíčku NuGet pomocí nástrojů pro různé platformy</span><span class="sxs-lookup"><span data-stu-id="a3496-263">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="a3496-264">Publikování balíčků</span><span class="sxs-lookup"><span data-stu-id="a3496-264">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="a3496-265">Úložiště balíčků modulu runtime</span><span class="sxs-lookup"><span data-stu-id="a3496-265">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="a3496-266">Složky bin projektu</span><span class="sxs-lookup"><span data-stu-id="a3496-266">Project bin folder</span></span>

<span data-ttu-id="a3496-267">Hostování rozšíření po spuštění můžete zadat *bin*– nasazení sestavení do vylepšené aplikace.</span><span class="sxs-lookup"><span data-stu-id="a3496-267">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="a3496-268">Hostování spuštění typy poskytované sestavení jsou k dispozici do aplikace pomocí jedné z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="a3496-268">The hosting startup types provided by the assembly are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="a3496-269">Soubor projektu vylepšené aplikace je odkaz na sestavení pro spouštění hostování (kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="a3496-269">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="a3496-270">Kompilace odkazem na místě, hostování při spuštění sestavení a všechny jeho závislosti jsou začleněny do závislostí souboru aplikace (*\*. deps.json*).</span><span class="sxs-lookup"><span data-stu-id="a3496-270">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*\*.deps.json*).</span></span> <span data-ttu-id="a3496-271">Tento postup platí, pokud scénář nasazení volá pro přesun sestavení zkompilované hostování spuštění knihovny (DLL soubor) náročné projektu nebo do umístění přístupné náročné projektem a se kompilace odkazuje na hostování pro spuštění sestavení.</span><span class="sxs-lookup"><span data-stu-id="a3496-271">This approach applies when the deployment scenario calls for moving the compiled hosting startup library's assembly (DLL file) to the consuming project or to a location accessible by the consuming project and a compile-time reference is made to the hosting startup's assembly.</span></span>
* <span data-ttu-id="a3496-272">Hostování spouštěcí závislosti soubor je k dispozici do vylepšené aplikace, jak je popsáno v [úložiště modulu Runtime](#runtime-store) část (bez kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="a3496-272">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

## <a name="sample-code"></a><span data-ttu-id="a3496-273">Ukázkový kód</span><span class="sxs-lookup"><span data-stu-id="a3496-273">Sample code</span></span>

<span data-ttu-id="a3496-274">[Ukázkový kód](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([stažení](xref:tutorials/index#how-to-download-a-sample)) ukazuje implementaci scénáře hostingu po spuštění:</span><span class="sxs-lookup"><span data-stu-id="a3496-274">The [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:tutorials/index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="a3496-275">Dvě hostování při spuštění sestavení (knihoven tříd) nastavit pár konfigurace v paměti páry klíč hodnota každé:</span><span class="sxs-lookup"><span data-stu-id="a3496-275">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="a3496-276">Balíček NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="a3496-276">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="a3496-277">Knihovna tříd (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="a3496-277">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="a3496-278">Hostování spuštění je aktivováno z úložiště nasazení sestavení modulu runtime (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="a3496-278">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="a3496-279">Sestavení přidá do aplikace při spuštění obsahující diagnostické informace o dvou middlewares:</span><span class="sxs-lookup"><span data-stu-id="a3496-279">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="a3496-280">Registrované služby</span><span class="sxs-lookup"><span data-stu-id="a3496-280">Registered services</span></span>
  * <span data-ttu-id="a3496-281">Adresa (schéma, hostitele, základ cesty, cesta, řetězec dotazu)</span><span class="sxs-lookup"><span data-stu-id="a3496-281">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="a3496-282">Připojení (vzdálenou IP adresu, vzdálený port, místní IP adresu, místní port, klientského certifikátu)</span><span class="sxs-lookup"><span data-stu-id="a3496-282">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="a3496-283">Hlavičky žádosti</span><span class="sxs-lookup"><span data-stu-id="a3496-283">Request headers</span></span>
  * <span data-ttu-id="a3496-284">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="a3496-284">Environment variables</span></span>

<span data-ttu-id="a3496-285">Ke spuštění ukázky:</span><span class="sxs-lookup"><span data-stu-id="a3496-285">To run the sample:</span></span>

<span data-ttu-id="a3496-286">**Aktivace z balíčku NuGet**</span><span class="sxs-lookup"><span data-stu-id="a3496-286">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="a3496-287">Kompilace *HostingStartupPackage* balíček s [balíčku dotnet](/dotnet/core/tools/dotnet-pack) příkazu.</span><span class="sxs-lookup"><span data-stu-id="a3496-287">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="a3496-288">Přidat název sestavení daného balíčku *HostingStartupPackage* k `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="a3496-288">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="a3496-289">Kompilace a spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="a3496-289">Compile and run the app.</span></span> <span data-ttu-id="a3496-290">Odkaz na balíček je k dispozici vylepšené aplikace (kompilace odkaz).</span><span class="sxs-lookup"><span data-stu-id="a3496-290">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="a3496-291">A `<PropertyGroup>` v projektu aplikace soubor Určuje výstup projektu balíček (*... / HostingStartupPackage/bin/Debug*) jako zdroj balíčků.</span><span class="sxs-lookup"><span data-stu-id="a3496-291">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="a3496-292">To umožňuje aplikacím používat balíček bez nahrává se balíček, který má [nuget.org](https://www.nuget.org/). Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="a3496-292">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```
1. <span data-ttu-id="a3496-293">Podívejte se, že hodnoty klíče konfigurace služby vykreslen metodou indexovou stránku shodovat s hodnotami nastavenými balíček `ServiceKeyInjection.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="a3496-293">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="a3496-294">Pokud provedete změny *HostingStartupPackage* projektu a ji znovu zkompilovat, zrušte zaškrtnutí políčka místní mezipaměti balíčku NuGet a zkontrolujte, že *HostingStartupApp* obdrží aktualizovaný balíček a nejsou zastaralé balíček z místní mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="a3496-294">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="a3496-295">Pokud chcete vymazat místní mezipaměť NuGet, spusťte následující [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) příkaz:</span><span class="sxs-lookup"><span data-stu-id="a3496-295">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```console
dotnet nuget locals all --clear
```

<span data-ttu-id="a3496-296">**Aktivace z knihovny tříd**</span><span class="sxs-lookup"><span data-stu-id="a3496-296">**Activation from a class library**</span></span>

1. <span data-ttu-id="a3496-297">Kompilace *HostingStartupLibrary* knihovny tříd pomocí [dotnet sestavení](/dotnet/core/tools/dotnet-build) příkazu.</span><span class="sxs-lookup"><span data-stu-id="a3496-297">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="a3496-298">Přidat název sestavení knihovny tříd *HostingStartupLibrary* k `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="a3496-298">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="a3496-299">*bin*– sestavení knihovny tříd aplikace nasadíte tak, že zkopírujete *HostingStartupLibrary.dll* soubor z knihovny tříd zkompilován výstup do aplikace *bin/Debug* složky.</span><span class="sxs-lookup"><span data-stu-id="a3496-299">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="a3496-300">Kompilace a spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="a3496-300">Compile and run the app.</span></span> <span data-ttu-id="a3496-301">`<ItemGroup>` Soubor v projektu aplikace odkazuje na sestavení knihovny tříd (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (referenční dokumentace kompilace).</span><span class="sxs-lookup"><span data-stu-id="a3496-301">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="a3496-302">Další informace naleznete v poznámkách v souboru projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="a3496-302">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```
1. <span data-ttu-id="a3496-303">Podívejte se, že hodnoty klíče konfigurace služby vykreslen metodou indexovou stránku odpovídat hodnoty nastavené v knihovně tříd `ServiceKeyInjection.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="a3496-303">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="a3496-304">**Aktivace z úložiště nasazení sestavení modulu runtime**</span><span class="sxs-lookup"><span data-stu-id="a3496-304">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="a3496-305">*StartupDiagnostics* používá projekt [PowerShell](/powershell/scripting/powershell-scripting) k úpravě jeho *StartupDiagnostics.deps.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="a3496-305">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="a3496-306">Prostředí PowerShell se instaluje standardně na Windows od verze Windows 7 SP1 a Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="a3496-306">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="a3496-307">Získat PowerShell na jiných platformách, naleznete v tématu [instalace prostředí Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).</span><span class="sxs-lookup"><span data-stu-id="a3496-307">To obtain PowerShell on other platforms, see [Installing Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).</span></span>
1. <span data-ttu-id="a3496-308">Sestavení *StartupDiagnostics* projektu.</span><span class="sxs-lookup"><span data-stu-id="a3496-308">Build the *StartupDiagnostics* project.</span></span> <span data-ttu-id="a3496-309">Po projekt je sestaven, cíl sestavení v souboru projektu automaticky:</span><span class="sxs-lookup"><span data-stu-id="a3496-309">After the project is built, a build target in the project file automatically:</span></span>
   * <span data-ttu-id="a3496-310">Spustí skript prostředí PowerShell k úpravě *StartupDiagnostics.deps.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="a3496-310">Triggers the PowerShell script to modify the *StartupDiagnostics.deps.json* file.</span></span>
   * <span data-ttu-id="a3496-311">Přesune *StartupDiagnostics.deps.json* souboru do profilu uživatele *additionalDeps* složky.</span><span class="sxs-lookup"><span data-stu-id="a3496-311">Moves the *StartupDiagnostics.deps.json* file to the user profile's *additionalDeps* folder.</span></span>
1. <span data-ttu-id="a3496-312">Spustit `dotnet store` příkaz command prompt v hostitelských spouštěcí adresář k uložení sestavení a jeho závislosti v úložišti profilu uživatele modulu runtime:</span><span class="sxs-lookup"><span data-stu-id="a3496-312">Execute the `dotnet store` command at a command prompt in the hosting startup's directory to store the assembly and its dependencies in the user profile's runtime store:</span></span>

   ```console
   dotnet store --manifest StartupDiagnostics.csproj --runtime <RID>
   ```
   <span data-ttu-id="a3496-313">Pro Windows, tento příkaz používá `win7-x64` [identifikátor modulu runtime (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="a3496-313">For Windows, the command uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="a3496-314">Při poskytování hostitelských po spuštění pro jiný modul runtime, nahraďte správné identifikátorů RID.</span><span class="sxs-lookup"><span data-stu-id="a3496-314">When providing the hosting startup for a different runtime, substitute the correct RID.</span></span>
1. <span data-ttu-id="a3496-315">Nastavte proměnné prostředí:</span><span class="sxs-lookup"><span data-stu-id="a3496-315">Set the environment variables:</span></span>
   * <span data-ttu-id="a3496-316">Přidat název sestavení *StartupDiagnostics* k `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="a3496-316">Add the assembly name of *StartupDiagnostics* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="a3496-317">Na Windows, nastavte `DOTNET_ADDITIONAL_DEPS` proměnnou prostředí, aby `%UserProfile%\.dotnet\x64\additionalDeps\StartupDiagnostics\`.</span><span class="sxs-lookup"><span data-stu-id="a3496-317">On Windows, set the `DOTNET_ADDITIONAL_DEPS` environment variable to `%UserProfile%\.dotnet\x64\additionalDeps\StartupDiagnostics\`.</span></span> <span data-ttu-id="a3496-318">V systému macOS nebo Linuxu, nastavte `DOTNET_ADDITIONAL_DEPS` proměnnou prostředí, aby `/Users/<USER>/.dotnet/x64/additionalDeps/StartupDiagnostics/`, kde `<USER>` tedy profil, který uživatel, který obsahuje spuštění hostování.</span><span class="sxs-lookup"><span data-stu-id="a3496-318">On macOS/Linux, set the `DOTNET_ADDITIONAL_DEPS` environment variable to `/Users/<USER>/.dotnet/x64/additionalDeps/StartupDiagnostics/`, where `<USER>` is the user profile that contains the hosting startup.</span></span>
1. <span data-ttu-id="a3496-319">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="a3496-319">Run the sample app.</span></span>
1. <span data-ttu-id="a3496-320">Žádosti `/services` koncový bod můžete zobrazit aplikace registrované služby.</span><span class="sxs-lookup"><span data-stu-id="a3496-320">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="a3496-321">Žádosti `/diag` koncový bod můžete zobrazit diagnostické informace.</span><span class="sxs-lookup"><span data-stu-id="a3496-321">Request the `/diag` endpoint to see the diagnostic information.</span></span>