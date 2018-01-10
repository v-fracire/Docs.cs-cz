# <a name="aspnet-core-url-rewriting-sample-aspnet-core-1x"></a><span data-ttu-id="34ca2-101">Adresa URL ASP.NET Core přepisování ukázkové (ASP.NET základní 1.x)</span><span class="sxs-lookup"><span data-stu-id="34ca2-101">ASP.NET Core URL Rewriting Sample (ASP.NET Core 1.x)</span></span>

<span data-ttu-id="34ca2-102">Tento příklad ukazuje využití ASP.NET Core 1.x Middleware přepisování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="34ca2-102">This sample illustrates usage of ASP.NET Core 1.x URL Rewriting Middleware.</span></span> <span data-ttu-id="34ca2-103">Aplikace se dozvíte adresy URL přesměrování a možnosti přepisování adres URL.</span><span class="sxs-lookup"><span data-stu-id="34ca2-103">The application demonstrates URL redirect and URL rewriting options.</span></span> <span data-ttu-id="34ca2-104">Ukázka 2.x ASP.NET Core, najdete v části [ASP.NET základní adresa URL přepisování ukázkové (ASP.NET Core 2.x)](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/2.x).</span><span class="sxs-lookup"><span data-stu-id="34ca2-104">For the ASP.NET Core 2.x sample, see [ASP.NET Core URL Rewriting Sample (ASP.NET Core 2.x)](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/2.x).</span></span>

<span data-ttu-id="34ca2-105">Při spuštění ukázky, odpověď se zpracuje zobrazující adresu URL přepsaná nebo přesměrované, když jedním z pravidel se použije k adrese URL žádosti.</span><span class="sxs-lookup"><span data-stu-id="34ca2-105">When running the sample, a response will be served that shows the rewritten or redirected URL when one of the rules is applied to a request URL.</span></span>

## <a name="examples-in-this-sample"></a><span data-ttu-id="34ca2-106">Příklady v této ukázce</span><span class="sxs-lookup"><span data-stu-id="34ca2-106">Examples in this sample</span></span>

* `AddRedirect("redirect-rule/(.*)", "$1")`
  - <span data-ttu-id="34ca2-107">Úspěch stavový kód: 302 (nalezeno)</span><span class="sxs-lookup"><span data-stu-id="34ca2-107">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="34ca2-108">Příklad (přesměrování): **/redirect-rule / {capture_group}** k **/redirected/ {capture_group}**</span><span class="sxs-lookup"><span data-stu-id="34ca2-108">Example (redirect): **/redirect-rule/{capture_group}** to **/redirected/{capture_group}**</span></span>
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - <span data-ttu-id="34ca2-109">Úspěch stavový kód: 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="34ca2-109">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="34ca2-110">Příklad (revize): **/rewrite-rule / {capture_group_1} / {capture_group_2}** k **/ přepsaná? var1 = {capture_group_1} & var2 = {capture_group_2}**</span><span class="sxs-lookup"><span data-stu-id="34ca2-110">Example (rewrite): **/rewrite-rule/{capture_group_1}/{capture_group_2}** to **/rewritten?var1={capture_group_1}&var2={capture_group_2}**</span></span>
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - <span data-ttu-id="34ca2-111">Úspěch stavový kód: 302 (nalezeno)</span><span class="sxs-lookup"><span data-stu-id="34ca2-111">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="34ca2-112">Příklad (přesměrování): **/apache-mod-rules-redirect / {capture_group}** k **/ přesměrováno? id = {capture_group}**</span><span class="sxs-lookup"><span data-stu-id="34ca2-112">Example (redirect): **/apache-mod-rules-redirect/{capture_group}** to **/redirected?id={capture_group}**</span></span>
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - <span data-ttu-id="34ca2-113">Úspěch stavový kód: 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="34ca2-113">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="34ca2-114">Příklad (revize): **/iis-rules-rewrite / {capture_group}** k **/ přepsaná? id = {capture_group}**</span><span class="sxs-lookup"><span data-stu-id="34ca2-114">Example (rewrite): **/iis-rules-rewrite/{capture_group}** to **/rewritten?id={capture_group}**</span></span>
* `Add(RedirectXMLRequests)`
  - <span data-ttu-id="34ca2-115">Úspěch stavový kód: 301 (trvale přesunut)</span><span class="sxs-lookup"><span data-stu-id="34ca2-115">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="34ca2-116">Příklad (přesměrování): **/file.xml** k **/xmlfiles/file.xml**</span><span class="sxs-lookup"><span data-stu-id="34ca2-116">Example (redirect): **/file.xml** to **/xmlfiles/file.xml**</span></span>
* `Add(new RedirectPNGRequests(".png", "/png-images")))`<br>`Add(new RedirectPNGRequests(".jpg", "/jpg-images")))`
  - <span data-ttu-id="34ca2-117">Úspěch stavový kód: 301 (trvale přesunut)</span><span class="sxs-lookup"><span data-stu-id="34ca2-117">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="34ca2-118">Příklad (přesměrování): **/image.png** k **/png-images/image.png**</span><span class="sxs-lookup"><span data-stu-id="34ca2-118">Example (redirect): **/image.png** to **/png-images/image.png**</span></span>
  - <span data-ttu-id="34ca2-119">Příklad (přesměrování): **/image.jpg** k **/jpg-images/image.jpg**</span><span class="sxs-lookup"><span data-stu-id="34ca2-119">Example (redirect): **/image.jpg** to **/jpg-images/image.jpg**</span></span>

## <a name="using-a-physicalfileprovider"></a><span data-ttu-id="34ca2-120">Použití`PhysicalFileProvider`</span><span class="sxs-lookup"><span data-stu-id="34ca2-120">Using a `PhysicalFileProvider`</span></span>
<span data-ttu-id="34ca2-121">Můžete také získat `IFileProvider` tak, že vytvoříte `PhysicalFileProvider` k předání do `AddApacheModRewrite()` a `AddIISUrlRewrite()` metody:</span><span class="sxs-lookup"><span data-stu-id="34ca2-121">You can also obtain an `IFileProvider` by creating a `PhysicalFileProvider` to pass into the `AddApacheModRewrite()` and `AddIISUrlRewrite()` methods:</span></span>
```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```
## <a name="secure-redirection-extensions"></a><span data-ttu-id="34ca2-122">Zabezpečené rozšíření přesměrování</span><span class="sxs-lookup"><span data-stu-id="34ca2-122">Secure redirection extensions</span></span>
<span data-ttu-id="34ca2-123">Tato ukázka obsahuje `WebHostBuilder` konfigurace pro aplikaci, aby používala adresy URL (**https://localhost:5001**, **https://localhost**) a testovací certifikát (**testCert.pfx**) pomoct v těchto zkoumat přesměrování metody.</span><span class="sxs-lookup"><span data-stu-id="34ca2-123">This sample includes `WebHostBuilder` configuration for the app to use URLs (**https://localhost:5001**, **https://localhost**) and a test certificate (**testCert.pfx**) to assist you in exploring these redirect methods.</span></span> <span data-ttu-id="34ca2-124">Přidat kterýkoliv z nich k `RewriteOptions()` v **Startup.cs** studovat jejich chování.</span><span class="sxs-lookup"><span data-stu-id="34ca2-124">Add any of them to the `RewriteOptions()` in **Startup.cs** to study their behavior.</span></span>

<span data-ttu-id="34ca2-125">Metoda</span><span class="sxs-lookup"><span data-stu-id="34ca2-125">Method</span></span> | <span data-ttu-id="34ca2-126">Stavový kód</span><span class="sxs-lookup"><span data-stu-id="34ca2-126">Status Code</span></span> | <span data-ttu-id="34ca2-127">port</span><span class="sxs-lookup"><span data-stu-id="34ca2-127">Port</span></span>
--- | :---: | :---:
`.AddRedirectToHttpsPermanent()` | <span data-ttu-id="34ca2-128">301</span><span class="sxs-lookup"><span data-stu-id="34ca2-128">301</span></span> | <span data-ttu-id="34ca2-129">Null (465)</span><span class="sxs-lookup"><span data-stu-id="34ca2-129">null (465)</span></span>
`.AddRedirectToHttps()` | <span data-ttu-id="34ca2-130">302</span><span class="sxs-lookup"><span data-stu-id="34ca2-130">302</span></span> | <span data-ttu-id="34ca2-131">Null (465)</span><span class="sxs-lookup"><span data-stu-id="34ca2-131">null (465)</span></span>
`.AddRedirectToHttps(301)` | <span data-ttu-id="34ca2-132">301</span><span class="sxs-lookup"><span data-stu-id="34ca2-132">301</span></span> | <span data-ttu-id="34ca2-133">Null (465)</span><span class="sxs-lookup"><span data-stu-id="34ca2-133">null (465)</span></span>
`.AddRedirectToHttps(301, 5001)` | <span data-ttu-id="34ca2-134">301</span><span class="sxs-lookup"><span data-stu-id="34ca2-134">301</span></span> | <span data-ttu-id="34ca2-135">5001</span><span class="sxs-lookup"><span data-stu-id="34ca2-135">5001</span></span>