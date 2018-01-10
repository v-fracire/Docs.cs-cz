---
uid: web-api/overview/odata-support-in-aspnet-web-api/odata-v3/creating-an-odata-endpoint
title: "Vytváření koncový bod OData v3 s Web API 2 | Microsoft Docs"
author: MikeWasson
description: "Open Data Protocol (OData) je protokol přístupu dat pro web. OData poskytuje uniform způsob, jak struktury dat, dotaz na data a manipulovat s daty..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/25/2014
ms.topic: article
ms.assetid: 262843d6-43a2-4f1c-82d9-0b90ae6df0cf
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/odata-v3/creating-an-odata-endpoint
msc.type: authoredcontent
ms.openlocfilehash: cb466124aacf6b13c1ade22ad8b865b83e6351e2
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2017
---
<a name="creating-an-odata-v3-endpoint-with-web-api-2"></a><span data-ttu-id="f21ea-104">Vytváření koncový bod OData v3 s Web API 2</span><span class="sxs-lookup"><span data-stu-id="f21ea-104">Creating an OData v3 Endpoint with Web API 2</span></span>
====================
<span data-ttu-id="f21ea-105">podle [Wasson Jan](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="f21ea-105">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

[<span data-ttu-id="f21ea-106">Stáhněte si dokončený projekt</span><span class="sxs-lookup"><span data-stu-id="f21ea-106">Download Completed Project</span></span>](http://code.msdn.microsoft.com/ASPNET-Web-API-OData-cecdb524)

> <span data-ttu-id="f21ea-107">[Protokol](http://www.odata.org/) (OData) je protokol přístupu dat pro web.</span><span class="sxs-lookup"><span data-stu-id="f21ea-107">The [Open Data Protocol](http://www.odata.org/) (OData) is a data access protocol for the web.</span></span> <span data-ttu-id="f21ea-108">Poskytuje jednotným způsobem struktury dat, dotaz na data a manipulovat s sadu dat prostřednictvím operace CRUD OData (vytvářet, číst, aktualizovat a odstranit).</span><span class="sxs-lookup"><span data-stu-id="f21ea-108">OData provides a uniform way to structure data, query the data, and manipulate the data set through CRUD operations (create, read, update, and delete).</span></span> <span data-ttu-id="f21ea-109">OData podporuje jak formáty JSON a AtomPub (XML).</span><span class="sxs-lookup"><span data-stu-id="f21ea-109">OData supports both AtomPub (XML) and JSON formats.</span></span> <span data-ttu-id="f21ea-110">OData také definuje způsob, jak vystavit metadata o datech.</span><span class="sxs-lookup"><span data-stu-id="f21ea-110">OData also defines a way to expose metadata about the data.</span></span> <span data-ttu-id="f21ea-111">Klienti mohou používat metadata ke zjištění informací o typu a vztahů pro datovou sadu.</span><span class="sxs-lookup"><span data-stu-id="f21ea-111">Clients can use the metadata to discover the type information and relationships for the data set.</span></span>
> 
> <span data-ttu-id="f21ea-112">Rozhraní ASP.NET Web API usnadňuje vytvořit koncový bod OData pro datovou sadu.</span><span class="sxs-lookup"><span data-stu-id="f21ea-112">ASP.NET Web API makes it easy to create an OData endpoint for a data set.</span></span> <span data-ttu-id="f21ea-113">Můžete ovládat, přesně OData operací, které podporuje koncový bod.</span><span class="sxs-lookup"><span data-stu-id="f21ea-113">You can control exactly which OData operations the endpoint supports.</span></span> <span data-ttu-id="f21ea-114">Je možné hostovat několik koncových bodů protokolu OData, spolu s koncových bodů protokolu OData.</span><span class="sxs-lookup"><span data-stu-id="f21ea-114">You can host multiple OData endpoints, alongside non-OData endpoints.</span></span> <span data-ttu-id="f21ea-115">Máte plnou kontrolu nad vašeho datového modelu, back-end obchodní logiky a datové vrstvy.</span><span class="sxs-lookup"><span data-stu-id="f21ea-115">You have full control over your data model, back-end business logic, and data layer.</span></span>
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="f21ea-116">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="f21ea-116">Software versions used in the tutorial</span></span>
> 
> 
> - [<span data-ttu-id="f21ea-117">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="f21ea-117">Visual Studio 2013</span></span>](https://www.microsoft.com/visualstudio/eng/2013-downloads)
> - <span data-ttu-id="f21ea-118">Webové rozhraní API 2</span><span class="sxs-lookup"><span data-stu-id="f21ea-118">Web API 2</span></span>
> - <span data-ttu-id="f21ea-119">OData verze 3</span><span class="sxs-lookup"><span data-stu-id="f21ea-119">OData Version 3</span></span>
> - <span data-ttu-id="f21ea-120">Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="f21ea-120">Entity Framework 6</span></span>
> - [<span data-ttu-id="f21ea-121">Webovou aplikaci Fiddler ladění Proxy (volitelné)</span><span class="sxs-lookup"><span data-stu-id="f21ea-121">Fiddler Web Debugging Proxy (Optional)</span></span>](http://www.fiddler2.com)
> 
> <span data-ttu-id="f21ea-122">Přidala se podpora web API OData v [ASP.NET a webové nástroje 2012.2 aktualizace](https://go.microsoft.com/fwlink/?LinkId=282650).</span><span class="sxs-lookup"><span data-stu-id="f21ea-122">Web API OData support was added in [ASP.NET and Web Tools 2012.2 Update](https://go.microsoft.com/fwlink/?LinkId=282650).</span></span> <span data-ttu-id="f21ea-123">Tento kurz používá však generování uživatelského rozhraní, která byla přidána v sadě Visual Studio 2013.</span><span class="sxs-lookup"><span data-stu-id="f21ea-123">However, this tutorial uses scaffolding that was added in Visual Studio 2013.</span></span>


<span data-ttu-id="f21ea-124">V tomto kurzu vytvoříte jednoduchý koncový bod OData, který klienti mohou odesílat dotazy.</span><span class="sxs-lookup"><span data-stu-id="f21ea-124">In this tutorial, you will create a simple OData endpoint that clients can query.</span></span> <span data-ttu-id="f21ea-125">Pokud vytvoříte klienta C# pro koncový bod.</span><span class="sxs-lookup"><span data-stu-id="f21ea-125">You will also create a C# client for the endpoint.</span></span> <span data-ttu-id="f21ea-126">Po dokončení tohoto kurzu sadu další kurzy ukazují, jak přidat další funkce, včetně vztahy entit, akce, a vyberte možnost rozbalte $/ $.</span><span class="sxs-lookup"><span data-stu-id="f21ea-126">After you complete this tutorial, the next set of tutorials show how to add more functionality, including entity relations, actions, and $expand/$select.</span></span>

- [<span data-ttu-id="f21ea-127">Vytvoření projektu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f21ea-127">Create the Visual Studio Project</span></span>](#create-project)
- [<span data-ttu-id="f21ea-128">Přidat Entity Model</span><span class="sxs-lookup"><span data-stu-id="f21ea-128">Add an Entity Model</span></span>](#add-model)
- [<span data-ttu-id="f21ea-129">Přidání Kontroleru OData</span><span class="sxs-lookup"><span data-stu-id="f21ea-129">Add an OData Controller</span></span>](#add-controller)
- [<span data-ttu-id="f21ea-130">Přidejte EDM a trasy</span><span class="sxs-lookup"><span data-stu-id="f21ea-130">Add the EDM and Route</span></span>](#edm)
- [<span data-ttu-id="f21ea-131">Počáteční hodnoty databázi (volitelné)</span><span class="sxs-lookup"><span data-stu-id="f21ea-131">Seed the Database (Optional)</span></span>](#seed-db)
- [<span data-ttu-id="f21ea-132">Zkoumání koncový bod OData</span><span class="sxs-lookup"><span data-stu-id="f21ea-132">Exploring the OData Endpoint</span></span>](#explore)
- [<span data-ttu-id="f21ea-133">Formáty serializace OData</span><span class="sxs-lookup"><span data-stu-id="f21ea-133">OData Serialization Formats</span></span>](#formats)

<a id="create-project"></a>
## <a name="create-the-visual-studio-project"></a><span data-ttu-id="f21ea-134">Vytvoření projektu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f21ea-134">Create the Visual Studio Project</span></span>

<span data-ttu-id="f21ea-135">V tomto kurzu vytvoříte koncový bod OData, který podporuje základní operace CRUD.</span><span class="sxs-lookup"><span data-stu-id="f21ea-135">In this tutorial, you will create an OData endpoint that supports basic CRUD operations.</span></span> <span data-ttu-id="f21ea-136">Koncový bod zveřejní jediný zdroj, seznam produktů.</span><span class="sxs-lookup"><span data-stu-id="f21ea-136">The endpoint will expose a single resource, a list of products.</span></span> <span data-ttu-id="f21ea-137">Další kurzy přidá další funkce.</span><span class="sxs-lookup"><span data-stu-id="f21ea-137">Later tutorials will add more features.</span></span>

<span data-ttu-id="f21ea-138">Spuštění sady Visual Studio a vyberte **nový projekt** z úvodní stránky.</span><span class="sxs-lookup"><span data-stu-id="f21ea-138">Start Visual Studio and select **New Project** from the Start page.</span></span> <span data-ttu-id="f21ea-139">Nebo z **soubor** nabídce vyberte možnost **nový** a potom **projektu**.</span><span class="sxs-lookup"><span data-stu-id="f21ea-139">Or, from the **File** menu, select **New** and then **Project**.</span></span>

<span data-ttu-id="f21ea-140">V **šablony** podokně, vyberte **nainstalovaných šablonách** a rozbalte uzel Visual C#.</span><span class="sxs-lookup"><span data-stu-id="f21ea-140">In the **Templates** pane, select **Installed Templates** and expand the Visual C# node.</span></span> <span data-ttu-id="f21ea-141">V části **Visual C#**, vyberte **webové**.</span><span class="sxs-lookup"><span data-stu-id="f21ea-141">Under **Visual C#**, select **Web**.</span></span> <span data-ttu-id="f21ea-142">Vyberte **webové aplikace ASP.NET** šablony.</span><span class="sxs-lookup"><span data-stu-id="f21ea-142">Select **the ASP.NET Web Application** template.</span></span>

![](creating-an-odata-endpoint/_static/image1.png)

<span data-ttu-id="f21ea-143">V **nový projekt ASP.NET** dialogovém okně, vyberte **prázdný** šablony.</span><span class="sxs-lookup"><span data-stu-id="f21ea-143">In the **New ASP.NET Project** dialog, select the **Empty** template.</span></span> <span data-ttu-id="f21ea-144">V části &quot;přidat složky a odkazy na základní... &quot;, zkontrolujte **webového rozhraní API**.</span><span class="sxs-lookup"><span data-stu-id="f21ea-144">Under &quot;Add folders and core references for...&quot;, check **Web API**.</span></span> <span data-ttu-id="f21ea-145">Click **OK**.</span><span class="sxs-lookup"><span data-stu-id="f21ea-145">Click **OK**.</span></span>

![](creating-an-odata-endpoint/_static/image2.png)

<a id="add-model"></a>
## <a name="add-an-entity-model"></a><span data-ttu-id="f21ea-146">Přidat Entity Model</span><span class="sxs-lookup"><span data-stu-id="f21ea-146">Add an Entity Model</span></span>

<span data-ttu-id="f21ea-147">A *modelu* je objekt, který představuje data ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f21ea-147">A *model* is an object that represents the data in your application.</span></span> <span data-ttu-id="f21ea-148">V tomto kurzu potřebujeme model, který představuje produkt.</span><span class="sxs-lookup"><span data-stu-id="f21ea-148">For this tutorial, we need a model that represents a product.</span></span> <span data-ttu-id="f21ea-149">Model odpovídá naše OData typu entity.</span><span class="sxs-lookup"><span data-stu-id="f21ea-149">The model corresponds to our OData entity type.</span></span>

<span data-ttu-id="f21ea-150">V Průzkumníku řešení klikněte pravým tlačítkem na složku modely.</span><span class="sxs-lookup"><span data-stu-id="f21ea-150">In Solution Explorer, right-click the Models folder.</span></span> <span data-ttu-id="f21ea-151">V místní nabídce vyberte **přidat** vyberte **třída**.</span><span class="sxs-lookup"><span data-stu-id="f21ea-151">From the context menu, select **Add** then select **Class**.</span></span>

![](creating-an-odata-endpoint/_static/image3.png)

<span data-ttu-id="f21ea-152">V **přidat nové** položky dialogové okno, název třídy &quot;produktu&quot;.</span><span class="sxs-lookup"><span data-stu-id="f21ea-152">In the **Add New** Item dialog, name the class &quot;Product&quot;.</span></span>

![](creating-an-odata-endpoint/_static/image4.png)

> [!NOTE]
> <span data-ttu-id="f21ea-153">Podle konvence třídy modelu jsou umístěny ve složce modelů.</span><span class="sxs-lookup"><span data-stu-id="f21ea-153">By convention, model classes are placed in the Models folder.</span></span> <span data-ttu-id="f21ea-154">Nemáte postupujte podle touto konvencí ve vašich vlastních projektů, ale použijeme ho v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="f21ea-154">You don't have to follow this convention in your own projects, but we'll use it for this tutorial.</span></span>


<span data-ttu-id="f21ea-155">V souboru Product.cs přidejte následující definici třídy:</span><span class="sxs-lookup"><span data-stu-id="f21ea-155">In the Product.cs file, add the following class definition:</span></span>

[!code-csharp[Main](creating-an-odata-endpoint/samples/sample1.cs)]

<span data-ttu-id="f21ea-156">Vlastnost ID bude klíč entity.</span><span class="sxs-lookup"><span data-stu-id="f21ea-156">The ID property will be the entity key.</span></span> <span data-ttu-id="f21ea-157">Klienti mohou odesílat dotazy produkty podle ID.</span><span class="sxs-lookup"><span data-stu-id="f21ea-157">Clients can query products by ID.</span></span> <span data-ttu-id="f21ea-158">Toto pole by také primární klíč v databázi back-end.</span><span class="sxs-lookup"><span data-stu-id="f21ea-158">This field would also be the primary key in the back-end database.</span></span>

<span data-ttu-id="f21ea-159">Nyní sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="f21ea-159">Build the project now.</span></span> <span data-ttu-id="f21ea-160">V dalším kroku použijeme některé sady Visual Studio generování uživatelského rozhraní, která používá reflexe najít typ produktu.</span><span class="sxs-lookup"><span data-stu-id="f21ea-160">In the next step, we'll use some Visual Studio scaffolding that uses reflection to find the Product type.</span></span>

<a id="add-controller"></a>
## <a name="add-an-odata-controller"></a><span data-ttu-id="f21ea-161">Přidání Kontroleru OData</span><span class="sxs-lookup"><span data-stu-id="f21ea-161">Add an OData Controller</span></span>

<span data-ttu-id="f21ea-162">A *řadič* je třída, která zpracovává požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="f21ea-162">A *controller* is a class that handles HTTP requests.</span></span> <span data-ttu-id="f21ea-163">Definujete samostatné řadič pro každou sadu entit v jste službu OData.</span><span class="sxs-lookup"><span data-stu-id="f21ea-163">You define a separate controller for each entity set in you OData service.</span></span> <span data-ttu-id="f21ea-164">V tomto kurzu vytvoříme jediného kontroleru.</span><span class="sxs-lookup"><span data-stu-id="f21ea-164">In this tutorial, we'll create a single controller.</span></span>

<span data-ttu-id="f21ea-165">V Průzkumníku řešení klikněte pravým tlačítkem složku řadiče.</span><span class="sxs-lookup"><span data-stu-id="f21ea-165">In Solution Explorer, right-click the the Controllers folder.</span></span> <span data-ttu-id="f21ea-166">Vyberte **přidat** a pak vyberte **řadič**.</span><span class="sxs-lookup"><span data-stu-id="f21ea-166">Select **Add** and then select **Controller**.</span></span>

![](creating-an-odata-endpoint/_static/image5.png)

<span data-ttu-id="f21ea-167">V **přidat vygenerované uživatelské rozhraní** dialogovém okně, vyberte &quot;webové rozhraní API 2 kontroler OData s akcemi používající rozhraní Entity Framework&quot;.</span><span class="sxs-lookup"><span data-stu-id="f21ea-167">In the **Add Scaffold** dialog, select &quot;Web API 2 OData Controller with actions, using Entity Framework&quot;.</span></span>

![](creating-an-odata-endpoint/_static/image6.png)

<span data-ttu-id="f21ea-168">V **přidat kontroler** dialogové okno, názvu kontroleru "ProductsController".</span><span class="sxs-lookup"><span data-stu-id="f21ea-168">In the **Add Controller** dialog, name the controller "ProductsController".</span></span> <span data-ttu-id="f21ea-169">Vyberte &quot;použít asynchronní akce kontroleru&quot; zaškrtávací políčko.</span><span class="sxs-lookup"><span data-stu-id="f21ea-169">Select the &quot;Use async controller actions&quot; checkbox.</span></span> <span data-ttu-id="f21ea-170">V **modelu** rozevíracího seznamu, vyberte třídu produktu.</span><span class="sxs-lookup"><span data-stu-id="f21ea-170">In the **Model** drop-down list, select the Product class.</span></span>

![](creating-an-odata-endpoint/_static/image7.png)

<span data-ttu-id="f21ea-171">Klikněte **nový kontext data...**  tlačítko.</span><span class="sxs-lookup"><span data-stu-id="f21ea-171">Click the **New data context...** button.</span></span> <span data-ttu-id="f21ea-172">Ponechte výchozí název pro datový typ kontextu a klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="f21ea-172">Leave the default name for the data context type, and click **Add**.</span></span>

![](creating-an-odata-endpoint/_static/image8.png)

<span data-ttu-id="f21ea-173">V dialogovém okně Přidat kontroler, přidat kontroler, klikněte na tlačítko Přidat.</span><span class="sxs-lookup"><span data-stu-id="f21ea-173">Click Add in the Add Controller dialog to add the controller.</span></span>

![](creating-an-odata-endpoint/_static/image9.png)

<span data-ttu-id="f21ea-174">Poznámka: Pokud se zobrazí chybové hlášení, uvádí &quot;došlo k chybě při načítání typu... &quot;, ujistěte se, jste vytvořili projekt Visual Studio, po přidání třídě produktu.</span><span class="sxs-lookup"><span data-stu-id="f21ea-174">Note: If you get an error message that says &quot;There was an error getting the type...&quot;, make sure that you built the Visual Studio project after you added the Product class.</span></span> <span data-ttu-id="f21ea-175">Generování reflexe používá k nalezení třídy.</span><span class="sxs-lookup"><span data-stu-id="f21ea-175">The scaffolding uses reflection to find the class.</span></span>

![](creating-an-odata-endpoint/_static/image10.png)

<span data-ttu-id="f21ea-176">Generování uživatelského rozhraní přidá do projektu dva soubory kódu:</span><span class="sxs-lookup"><span data-stu-id="f21ea-176">The scaffolding adds two code files to the project:</span></span>

- <span data-ttu-id="f21ea-177">Products.cs definuje kontroleru webového rozhraní API, který implementuje koncový bod OData.</span><span class="sxs-lookup"><span data-stu-id="f21ea-177">Products.cs defines the Web API controller that implements the OData endpoint.</span></span>
- <span data-ttu-id="f21ea-178">ProductServiceContext.cs poskytuje metody pro dotaz na základní databázi pomocí Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="f21ea-178">ProductServiceContext.cs provides methods to query the underlying database, using Entity Framework.</span></span>

![](creating-an-odata-endpoint/_static/image11.png)

<a id="edm"></a>
## <a name="add-the-edm-and-route"></a><span data-ttu-id="f21ea-179">Přidejte EDM a trasy</span><span class="sxs-lookup"><span data-stu-id="f21ea-179">Add the EDM and Route</span></span>

<span data-ttu-id="f21ea-180">V Průzkumníku řešení rozbalte aplikace\_spustit složku a otevřete soubor s názvem WebApiConfig.cs.</span><span class="sxs-lookup"><span data-stu-id="f21ea-180">In Solution Explorer, expand the App\_Start folder and open the file named WebApiConfig.cs.</span></span> <span data-ttu-id="f21ea-181">Tato třída obsahuje kód konfigurace pro webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="f21ea-181">This class holds configuration code for Web API.</span></span> <span data-ttu-id="f21ea-182">Nahraďte tento kód s následujícími službami:</span><span class="sxs-lookup"><span data-stu-id="f21ea-182">Replace this code with the following:</span></span>

[!code-csharp[Main](creating-an-odata-endpoint/samples/sample2.cs)]

<span data-ttu-id="f21ea-183">Tento kód provede dvě věci:</span><span class="sxs-lookup"><span data-stu-id="f21ea-183">This code does two things:</span></span>

- <span data-ttu-id="f21ea-184">Vytvoří modelu Entity Data Model (EDM) pro koncový bod OData.</span><span class="sxs-lookup"><span data-stu-id="f21ea-184">Creates an Entity Data Model (EDM) for the OData endpoint.</span></span>
- <span data-ttu-id="f21ea-185">Přidá trasy pro koncový bod.</span><span class="sxs-lookup"><span data-stu-id="f21ea-185">Adds a route for the endpoint.</span></span>

<span data-ttu-id="f21ea-186">EDM je abstraktní model dat.</span><span class="sxs-lookup"><span data-stu-id="f21ea-186">An EDM is an abstract model of the data.</span></span> <span data-ttu-id="f21ea-187">EDM slouží k vytvoření dokument metadat a zadejte identifikátory URI pro službu.</span><span class="sxs-lookup"><span data-stu-id="f21ea-187">The EDM is used to create the metadata document and define the URIs for the service.</span></span> <span data-ttu-id="f21ea-188">**ODataConventionModelBuilder** vytvoří EDM pomocí sadu výchozí zásady vytváření názvů EDM.</span><span class="sxs-lookup"><span data-stu-id="f21ea-188">The **ODataConventionModelBuilder** creates an EDM by using a set of default naming conventions EDM.</span></span> <span data-ttu-id="f21ea-189">Tento postup vyžaduje minimálně kódu.</span><span class="sxs-lookup"><span data-stu-id="f21ea-189">This approach requires the least code.</span></span> <span data-ttu-id="f21ea-190">Pokud chcete mít větší kontrolu nad EDM, můžete použít **Tvůrce ODataModelBuilder** třídy za účelem vytvoření modelu EDM přidáním vlastností, klíčů a navigačních vlastností explicitně.</span><span class="sxs-lookup"><span data-stu-id="f21ea-190">If you want more control over the EDM, you can use the **ODataModelBuilder** class to create the EDM by adding properties, keys, and navigation properties explicitly.</span></span>

<span data-ttu-id="f21ea-191">**EntitySet** metoda přidá k modelu EDM sadu entit:</span><span class="sxs-lookup"><span data-stu-id="f21ea-191">The **EntitySet** method adds an entity set to the EDM:</span></span>

[!code-csharp[Main](creating-an-odata-endpoint/samples/sample3.cs)]

<span data-ttu-id="f21ea-192">Řetězec "Produkty" definuje název sady entit.</span><span class="sxs-lookup"><span data-stu-id="f21ea-192">The string "Products" defines the name of the entity set.</span></span> <span data-ttu-id="f21ea-193">Název kontroleru musí odpovídat názvu sady entit.</span><span class="sxs-lookup"><span data-stu-id="f21ea-193">The name of the controller must match the name of the entity set.</span></span> <span data-ttu-id="f21ea-194">V tomto kurzu je sada entit s názvem "Produkty" a má název kontroleru `ProductsController`.</span><span class="sxs-lookup"><span data-stu-id="f21ea-194">In this tutorial, the entity set is named "Products" and the controller is named `ProductsController`.</span></span> <span data-ttu-id="f21ea-195">Pokud jste s názvem "ProductSet" sady entit, by název kontroleru `ProductSetController`.</span><span class="sxs-lookup"><span data-stu-id="f21ea-195">If you named the entity set "ProductSet", you would name the controller `ProductSetController`.</span></span> <span data-ttu-id="f21ea-196">Všimněte si, že koncový bod může mít několik sad entit.</span><span class="sxs-lookup"><span data-stu-id="f21ea-196">Note that an endpoint can have multiple entity sets.</span></span> <span data-ttu-id="f21ea-197">Volání **EntitySet&lt;T&gt;**  každé entity nastavení a potom zadejte odpovídající kontroler.</span><span class="sxs-lookup"><span data-stu-id="f21ea-197">Call **EntitySet&lt;T&gt;** for each entity set, and then define a corresponding controller.</span></span>

<span data-ttu-id="f21ea-198">**MapODataRoute** metoda přidá trasu pro koncový bod OData.</span><span class="sxs-lookup"><span data-stu-id="f21ea-198">The **MapODataRoute** method adds a route for the OData endpoint.</span></span>

[!code-csharp[Main](creating-an-odata-endpoint/samples/sample4.cs)]

<span data-ttu-id="f21ea-199">První parametr je popisný název pro tuto trasu.</span><span class="sxs-lookup"><span data-stu-id="f21ea-199">The first parameter is a friendly name for the route.</span></span> <span data-ttu-id="f21ea-200">Klienti služby nejsou vidět tento název.</span><span class="sxs-lookup"><span data-stu-id="f21ea-200">Clients of your service do not see this name.</span></span> <span data-ttu-id="f21ea-201">Druhý parametr je předpony identifikátoru URI pro koncový bod.</span><span class="sxs-lookup"><span data-stu-id="f21ea-201">The second parameter is the URI prefix for the endpoint.</span></span> <span data-ttu-id="f21ea-202">Daný tento kód, je identifikátor URI pro sadu entit produkty http://*hostname*  /odata/produkty.</span><span class="sxs-lookup"><span data-stu-id="f21ea-202">Given this code, the URI for the Products entity set is http://*hostname*/odata/Products.</span></span> <span data-ttu-id="f21ea-203">Vaše aplikace může mít více než jeden koncový bod OData.</span><span class="sxs-lookup"><span data-stu-id="f21ea-203">Your application can have more than one OData endpoint.</span></span> <span data-ttu-id="f21ea-204">Pro každý koncový bod volání **MapODataRoute** a zadejte název jedinečné trasy a jedinečný předpony identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="f21ea-204">For each endpoint, call **MapODataRoute** and provide a unique route name and a unique URI prefix.</span></span>

<a id="seed-db"></a>
## <a name="seed-the-database-optional"></a><span data-ttu-id="f21ea-205">Počáteční hodnoty databázi (volitelné)</span><span class="sxs-lookup"><span data-stu-id="f21ea-205">Seed the Database (Optional)</span></span>

<span data-ttu-id="f21ea-206">V tomto kroku použijete rozhraní Entity Framework počáteční hodnoty databáze s nějaká testovací data.</span><span class="sxs-lookup"><span data-stu-id="f21ea-206">In this step, you will use Entity Framework to seed the database with some test data.</span></span> <span data-ttu-id="f21ea-207">Tento krok je volitelný, ale umožňuje vám hned otestovat váš koncový bod OData.</span><span class="sxs-lookup"><span data-stu-id="f21ea-207">This step is optional, but it lets you test out your OData endpoint right away.</span></span>

<span data-ttu-id="f21ea-208">Z **nástroje** nabídce vyberte možnost **Správce balíčků knihoven**, pak vyberte **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="f21ea-208">From the **Tools** menu, select **Library Package Manager**, then select **Package Manager Console**.</span></span> <span data-ttu-id="f21ea-209">V okně konzoly Správce balíčků zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f21ea-209">In the Package Manager Console window, enter the following command:</span></span>

[!code-console[Main](creating-an-odata-endpoint/samples/sample5.cmd)]

<span data-ttu-id="f21ea-210">Tento postup přidá složku s názvem migrace a kód soubor s názvem Configuration.cs.</span><span class="sxs-lookup"><span data-stu-id="f21ea-210">This adds a folder named Migrations and a code file named Configuration.cs.</span></span>

![](creating-an-odata-endpoint/_static/image12.png)

<span data-ttu-id="f21ea-211">Umožňuje otevřít tento soubor a přidejte následující kód, který `Configuration.Seed` metoda.</span><span class="sxs-lookup"><span data-stu-id="f21ea-211">Open this file and add the following code to the `Configuration.Seed` method.</span></span>

[!code-csharp[Main](creating-an-odata-endpoint/samples/sample6.cs)]

<span data-ttu-id="f21ea-212">V okně konzoly Správce balíčků zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="f21ea-212">In the Package Manager Console Window, enter the following commands:</span></span>

[!code-console[Main](creating-an-odata-endpoint/samples/sample7.cmd)]

<span data-ttu-id="f21ea-213">Tyto příkazy Generovat kód, který vytvoří databázi a potom provede tento kód.</span><span class="sxs-lookup"><span data-stu-id="f21ea-213">These commands generate code that creates the database, and then executes that code.</span></span>

<a id="explore"></a>
## <a name="exploring-the-odata-endpoint"></a><span data-ttu-id="f21ea-214">Zkoumání koncový bod OData</span><span class="sxs-lookup"><span data-stu-id="f21ea-214">Exploring the OData Endpoint</span></span>

<span data-ttu-id="f21ea-215">V této části, použijeme [ladění Proxy webové aplikaci Fiddler](http://www.fiddler2.com) k odeslání požadavků na koncový bod a zkontrolujte zprávy odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f21ea-215">In this section, we'll use the [Fiddler Web Debugging Proxy](http://www.fiddler2.com) to send requests to the endpoint and examine the response messages.</span></span> <span data-ttu-id="f21ea-216">To vám pomůže Seznamte se s možnostmi koncový bod OData.</span><span class="sxs-lookup"><span data-stu-id="f21ea-216">This will help you to understand the capabilities of an OData endpoint.</span></span>

<span data-ttu-id="f21ea-217">Ve Visual Studiu stisknutím klávesy F5 spusťte ladění.</span><span class="sxs-lookup"><span data-stu-id="f21ea-217">In Visual Studio, press F5 to start debugging.</span></span> <span data-ttu-id="f21ea-218">Ve výchozím nastavení, Visual Studio otevře prohlížeč tak, aby `http://localhost:*port*`, kde *portu* je číslo portu, který je nakonfigurovaný v nastavení projektu.</span><span class="sxs-lookup"><span data-stu-id="f21ea-218">By default, Visual Studio opens your browser to `http://localhost:*port*`, where *port* is the port number configured in the project settings.</span></span>

<span data-ttu-id="f21ea-219">Můžete změnit číslo portu v nastavení projektu.</span><span class="sxs-lookup"><span data-stu-id="f21ea-219">You can change the port number in the project settings.</span></span> <span data-ttu-id="f21ea-220">V Průzkumníku řešení klikněte pravým tlačítkem na projekt a vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="f21ea-220">In Solution Explorer, right-click the project and select **Properties**.</span></span> <span data-ttu-id="f21ea-221">V okně vlastností vyberte **webové**.</span><span class="sxs-lookup"><span data-stu-id="f21ea-221">In the properties window, select **Web**.</span></span> <span data-ttu-id="f21ea-222">Zadejte číslo portu v části **adresa Url projektu**.</span><span class="sxs-lookup"><span data-stu-id="f21ea-222">Enter the port number under **Project Url**.</span></span>

### <a name="service-document"></a><span data-ttu-id="f21ea-223">Dokument služby</span><span class="sxs-lookup"><span data-stu-id="f21ea-223">Service Document</span></span>

<span data-ttu-id="f21ea-224">*Dokument služby* obsahuje seznam sad entit pro koncový bod OData.</span><span class="sxs-lookup"><span data-stu-id="f21ea-224">The *service document* contains a list of the entity sets for the OData endpoint.</span></span> <span data-ttu-id="f21ea-225">Získat dokument služby, odeslat požadavek GET na kořenové identifikátor URI služby.</span><span class="sxs-lookup"><span data-stu-id="f21ea-225">To get the service document, send a GET request to the root URI of the service.</span></span>

<span data-ttu-id="f21ea-226">Použijte aplikaci Fiddler, zadejte následující identifikátor URI v **autora** karta: `http://localhost:port/odata/`, kde *portu* je číslo portu.</span><span class="sxs-lookup"><span data-stu-id="f21ea-226">Using Fiddler, enter the following URI in the **Composer** tab: `http://localhost:port/odata/`, where *port* is the port number.</span></span>

![](creating-an-odata-endpoint/_static/image13.png)

<span data-ttu-id="f21ea-227">Klikněte **Execute** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="f21ea-227">Click the **Execute** button.</span></span> <span data-ttu-id="f21ea-228">Fiddler odešle požadavek HTTP GET do vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="f21ea-228">Fiddler sends an HTTP GET request to your application.</span></span> <span data-ttu-id="f21ea-229">Měli byste vidět odpovědi v seznamu webové relace.</span><span class="sxs-lookup"><span data-stu-id="f21ea-229">You should see the response in the Web Sessions list.</span></span> <span data-ttu-id="f21ea-230">Pokud se vše funguje stavový kód bude 200.</span><span class="sxs-lookup"><span data-stu-id="f21ea-230">If everything is working, the status code will be 200.</span></span>

![](creating-an-odata-endpoint/_static/image14.png)

<span data-ttu-id="f21ea-231">Dvakrát klikněte na odpovědi v seznamu webové relace a zobrazit podrobnosti zprávy s odpovědí na kartě kontroly.</span><span class="sxs-lookup"><span data-stu-id="f21ea-231">Double-click the response in the Web Sessions list to see the details of the response message in the Inspectors tab.</span></span>

![](creating-an-odata-endpoint/_static/image15.png)

<span data-ttu-id="f21ea-232">Nezpracovaná zprávy s odpovědí HTTP by měl vypadat takto:</span><span class="sxs-lookup"><span data-stu-id="f21ea-232">The raw HTTP response message should look similar to the following:</span></span>

[!code-console[Main](creating-an-odata-endpoint/samples/sample8.cmd)]

<span data-ttu-id="f21ea-233">Ve výchozím nastavení webového rozhraní API vrátí dokumentu služby ve formátu AtomPub.</span><span class="sxs-lookup"><span data-stu-id="f21ea-233">By default, Web API returns the service document in AtomPub format.</span></span> <span data-ttu-id="f21ea-234">Požádat o JSON, přidejte následující hlavičku požadavku HTTP:</span><span class="sxs-lookup"><span data-stu-id="f21ea-234">To request JSON, add the following header to the HTTP request:</span></span>

`Accept: application/json`

![](creating-an-odata-endpoint/_static/image16.png)

<span data-ttu-id="f21ea-235">Odpověď HTTP, která nyní obsahuje datovou část JSON:</span><span class="sxs-lookup"><span data-stu-id="f21ea-235">Now the HTTP response contains a JSON payload:</span></span>

[!code-console[Main](creating-an-odata-endpoint/samples/sample9.cmd)]

### <a name="service-metadata-document"></a><span data-ttu-id="f21ea-236">Dokument metadat služby</span><span class="sxs-lookup"><span data-stu-id="f21ea-236">Service Metadata Document</span></span>

<span data-ttu-id="f21ea-237">*Dokument metadat služby* popisuje datový model služby, pomocí jazyka XML nazývaného koncepční schéma definice Language (CSDL).</span><span class="sxs-lookup"><span data-stu-id="f21ea-237">The *service metadata document* describes the data model of the service, using an XML language called the Conceptual Schema Definition Language (CSDL).</span></span> <span data-ttu-id="f21ea-238">Dokument metadat ukazuje strukturu dat v rámci služby a slouží ke generování kódu klienta.</span><span class="sxs-lookup"><span data-stu-id="f21ea-238">The metadata document shows the structure of the data in the service, and can be used to generate client code.</span></span>

<span data-ttu-id="f21ea-239">Chcete-li získat dokument metadat, odešlete požadavek GET na `http://localhost:port/odata/$metadata`.</span><span class="sxs-lookup"><span data-stu-id="f21ea-239">To get the metadata document, send a GET request to `http://localhost:port/odata/$metadata`.</span></span> <span data-ttu-id="f21ea-240">Zde je metadata pro koncový bod uvedené v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="f21ea-240">Here is the metadata for the endpoint shown in this tutorial.</span></span>

[!code-console[Main](creating-an-odata-endpoint/samples/sample10.cmd)]

### <a name="entity-set"></a><span data-ttu-id="f21ea-241">Sada entit</span><span class="sxs-lookup"><span data-stu-id="f21ea-241">Entity Set</span></span>

<span data-ttu-id="f21ea-242">Chcete-li získat sadu entit produkty, odeslat požadavek GET na `http://localhost:port/odata/Products`.</span><span class="sxs-lookup"><span data-stu-id="f21ea-242">To get the Products entity set, send a GET request to `http://localhost:port/odata/Products`.</span></span>

[!code-console[Main](creating-an-odata-endpoint/samples/sample11.cmd)]

### <a name="entity"></a><span data-ttu-id="f21ea-243">Entity</span><span class="sxs-lookup"><span data-stu-id="f21ea-243">Entity</span></span>

<span data-ttu-id="f21ea-244">Potřebujete jednotlivých produktů, pošlete požadavek GET na `http://localhost:port/odata/Products(1)`, kde "1" je ID produktu.</span><span class="sxs-lookup"><span data-stu-id="f21ea-244">To get an individual product, send a GET request to `http://localhost:port/odata/Products(1)`, where "1" is the product ID.</span></span>

[!code-console[Main](creating-an-odata-endpoint/samples/sample12.cmd)]

<a id="formats"></a>
## <a name="odata-serialization-formats"></a><span data-ttu-id="f21ea-245">Formáty serializace OData</span><span class="sxs-lookup"><span data-stu-id="f21ea-245">OData Serialization Formats</span></span>

<span data-ttu-id="f21ea-246">OData podporuje několik formáty serializace:</span><span class="sxs-lookup"><span data-stu-id="f21ea-246">OData supports several serialization formats:</span></span>

- <span data-ttu-id="f21ea-247">Atom Pub (XML)</span><span class="sxs-lookup"><span data-stu-id="f21ea-247">Atom Pub (XML)</span></span>
- <span data-ttu-id="f21ea-248">JSON "light" (dostupné v OData v3)</span><span class="sxs-lookup"><span data-stu-id="f21ea-248">JSON "light" (introduced in OData v3)</span></span>
- <span data-ttu-id="f21ea-249">JSON "podrobné" (OData v2)</span><span class="sxs-lookup"><span data-stu-id="f21ea-249">JSON "verbose" (OData v2)</span></span>

<span data-ttu-id="f21ea-250">Ve výchozím nastavení používá formát "light" AtomPubJSON webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="f21ea-250">By default, Web API uses AtomPubJSON "light" format.</span></span> 

<span data-ttu-id="f21ea-251">Formát AtomPub získáte nastavte hlavičku Accept "application/atom + xml".</span><span class="sxs-lookup"><span data-stu-id="f21ea-251">To get AtomPub format, set the Accept header to "application/atom+xml".</span></span> <span data-ttu-id="f21ea-252">Tady je odpovědi na příkladu:</span><span class="sxs-lookup"><span data-stu-id="f21ea-252">Here is an example response body:</span></span>

[!code-console[Main](creating-an-odata-endpoint/samples/sample13.cmd)]

<span data-ttu-id="f21ea-253">Zobrazí jednu zřejmé nevýhodou formát Atom: je mnohem podrobnější než JSON light.</span><span class="sxs-lookup"><span data-stu-id="f21ea-253">You can see one obvious disadvantage of the Atom format: It's a lot more verbose than the JSON light.</span></span> <span data-ttu-id="f21ea-254">Ale pokud máte klienta, který jste srozuměni s tím AtomPub, klient může dáváte přednost tento formát přes JSON.</span><span class="sxs-lookup"><span data-stu-id="f21ea-254">However, if you have a client that understands AtomPub, the client might prefer that format over JSON.</span></span>

<span data-ttu-id="f21ea-255">Tady je JSON light verzi stejné entity:</span><span class="sxs-lookup"><span data-stu-id="f21ea-255">Here is the JSON light version of the same entity:</span></span>

[!code-console[Main](creating-an-odata-endpoint/samples/sample14.cmd)]

<span data-ttu-id="f21ea-256">Formát JSON light byla zavedena v protokolu OData verze 3.</span><span class="sxs-lookup"><span data-stu-id="f21ea-256">The JSON light format was introduced in version 3 of the OData protocol.</span></span> <span data-ttu-id="f21ea-257">Pro zpětnou kompatibilitu může klient požadovat starší "podrobný" formát JSON.</span><span class="sxs-lookup"><span data-stu-id="f21ea-257">For backward compatibility, a client can request the older "verbose" JSON format.</span></span> <span data-ttu-id="f21ea-258">Chcete-li požádat o podrobné JSON, nastavte hlavička Accept `application/json;odata=verbose`.</span><span class="sxs-lookup"><span data-stu-id="f21ea-258">To request verbose JSON, set the Accept header to `application/json;odata=verbose`.</span></span> <span data-ttu-id="f21ea-259">Zde je podrobný verze:</span><span class="sxs-lookup"><span data-stu-id="f21ea-259">Here is the verbose version:</span></span>

[!code-console[Main](creating-an-odata-endpoint/samples/sample15.cmd)]

<span data-ttu-id="f21ea-260">Tento formát přenese tak další metadata v textu odpovědi, které lze přidat značné režijní náklady za celou relace.</span><span class="sxs-lookup"><span data-stu-id="f21ea-260">This format conveys more metadata in the response body, which can add considerable overhead over an entire session.</span></span> <span data-ttu-id="f21ea-261">Kromě toho přidá úroveň dereference nástrojem pro zabalení objektu v vlastnost s názvem "d".</span><span class="sxs-lookup"><span data-stu-id="f21ea-261">Also, it adds a level of indirection by wrapping the object in a property named "d".</span></span>

## <a name="next-steps"></a><span data-ttu-id="f21ea-262">Další kroky</span><span class="sxs-lookup"><span data-stu-id="f21ea-262">Next Steps</span></span>

- [<span data-ttu-id="f21ea-263">Přidat vztahy entit</span><span class="sxs-lookup"><span data-stu-id="f21ea-263">Add Entity Relations</span></span>](working-with-entity-relations.md)
- [<span data-ttu-id="f21ea-264">Přidání akcí OData</span><span class="sxs-lookup"><span data-stu-id="f21ea-264">Add OData Actions</span></span>](odata-actions.md)
- [<span data-ttu-id="f21ea-265">Volání služby OData z klienta .NET</span><span class="sxs-lookup"><span data-stu-id="f21ea-265">Call the OData Service From a .NET Client</span></span>](calling-an-odata-service-from-a-net-client.md)