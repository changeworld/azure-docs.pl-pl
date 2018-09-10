---
title: Usługa Azure Functions, skalowanie i hosting | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dokonać wyboru między planu usługi Azure Functions zużycie i plan usługi App Service.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, funkcje, planu zużycie, plan usługi app service, przetwarzanie zdarzeń, elementów webhook, obliczanie dynamiczne, architektury bezserwerowej
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 08/09/2018
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b562b10be8add02d0a3c6eb95e8df8eb0711a208
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44093536"
---
# <a name="azure-functions-scale-and-hosting"></a>Funkcje Azure podlegają skalowaniu i hosting

Usługa Azure Functions jest uruchamiany w dwóch różnych trybach: plan zużycie i plan usługi Azure App Service. Plan zużycie automatycznie przydziela moc obliczeniową, gdy kod jest uruchomiony. Twoja aplikacja jest skalowana w poziomie, gdy trzeba obsłużyć obciążenie i skalowane w dół, gdy kod nie jest uruchomiony. Nie trzeba płacić za bezczynnych maszyn wirtualnych lub zarezerwować pojemności z wyprzedzeniem. Ten artykuł koncentruje się na plan zużycie [bezserwerowe](https://azure.microsoft.com/overview/serverless-computing/) modelu aplikacji. Aby uzyskać szczegółowe informacje dotyczące sposobu działania dedykowanego planu usługi App Service, zobacz [szczegółowe omówienie planów usługi Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

> [!NOTE]  
> [Hosting systemu Linux](functions-create-first-azure-function-azure-cli-linux.md) jest obecnie dostępna tylko w planie usługi App Service.

Jeśli nie znasz usługi Azure Functions, zobacz [omówienia usługi Azure Functions](functions-overview.md).

Podczas tworzenia aplikacji funkcji, możesz wybrać plan hostingu dla funkcji w aplikacji. W dowolnym planie wystąpienie *hosta usługi Azure Functions* wykonuje funkcje. Typ kontrolki plan:

* Jak wystąpień hosta są skalowane do wewnątrz.
* Zasoby, które są dostępne dla każdego hosta.

> [!IMPORTANT]
> Musisz wybrać typ planu hostingu podczas tworzenia aplikacji funkcji. Nie można zmienić go później.

W planie usługi App Service można skalować między warstwami można przydzielić różnych ilości zasobów. W planie zużycie usługi Azure Functions automatycznie obsługuje wszystkie alokacji zasobów. 

## <a name="consumption-plan"></a>Plan Zużycie

Podczas korzystania z planu zużycie wystąpień hosta usługi Azure Functions są dynamicznie dodawane i usuwane na podstawie liczby zdarzeń przychodzących. Ten plan bez użycia serwera skaluje się automatycznie, a opłaty są naliczane za zasoby obliczeniowe tylko wtedy, gdy funkcje są uruchomione. W ramach planu zużycie wykonywania funkcji upłynie limit czasu po upływie czasu określonego można konfigurować.

> [!NOTE]
> Domyślna wartość limitu czasu dla funkcji w ramach planu zużycie wynosi 5 minut. Wartość może zostać zwiększone dla aplikacji funkcji, maksymalnie do 10 minut, zmieniając właściwość `functionTimeout` w [host.json](functions-host-json.md#functiontimeout) pliku projektu.

Rozliczanie jest na podstawie liczby wykonań, czas wykonywania i używanej pamięci. Opłaty są agregowane w obrębie wszystkich funkcji w ramach aplikacji funkcji. Aby uzyskać więcej informacji, zobacz [Usługa Azure Functions, cennik].

Plan zużycie jest domyślny plan hostingu i zapewnia następujące korzyści:

* Płać tylko, gdy funkcje są uruchomione.
* Skalować automatycznie, nawet w okresach wysokiego obciążenia.

## <a name="app-service-plan"></a>Plan usługi App Service

W przypadku dedykowanego planu usługi App Service — funkcja działają na dedykowanych maszyn wirtualnych na podstawowa, standardowa, Premium i jednostek SKU izolowanej, która jest taka sama, jak inne aplikacje usługi App Service. Dedykowanych maszyn wirtualnych są przydzielane do aplikacji funkcji, która oznacza, że host funkcji może być [zawsze uruchomiona](#always-on). Plany usługi App Service obsługuje systemu Linux.

Należy wziąć pod uwagę plan usługi App Service w następujących przypadkach:

* Masz istniejące niedostatecznie używanych maszyn wirtualnych, które zostały już uruchomione inne wystąpienia usługi App Service.
* Twoje aplikacje funkcji Uruchom stale albo w praktycznie w sposób ciągły. W tym przypadku Plan usługi App Service może być bardziej ekonomiczne.
* Potrzebujesz więcej opcji procesora CPU lub pamięci niż jest podana w planu zużycie.
* Twój kod musi zostać uruchomiony dłużej niż maksymalny czas wykonywania dozwolone w planie zużycie, który jest maksymalnie 10 minut.
* Wymagane funkcje, które są dostępne tylko na plan usługi App Service, takie jak obsługa środowiska App Service Environment, łączność w sieci Wirtualnej/sieci VPN i większych rozmiarów maszyn wirtualnych.
* Aby uruchomić aplikację funkcji w systemie Linux lub chcesz udostępnić obraz niestandardowy, na którym chcesz uruchamiać swoje funkcje.

Maszynę wirtualną oddziela kosztów od liczby wykonań, czas wykonywania i używanej pamięci. W rezultacie nie płacisz za więcej niż koszt wystąpienia maszyny Wirtualnej, który przydzielasz. Aby uzyskać szczegółowe informacje dotyczące sposobu działania planu usługi App Service, zobacz [szczegółowe omówienie planów usługi Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Plan usługi App Service można ręcznie skalować w poziomie, dodając kolejne wystąpienia maszyn wirtualnych lub włączyć Skalowanie automatyczne. Aby uzyskać więcej informacji, zobacz [ręczne lub automatyczne skalowanie liczby wystąpień](../monitoring-and-diagnostics/monitoring-autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Możesz również skalować w górę, wybierając inny plan usługi App Service. Aby uzyskać więcej informacji, zobacz [skalowanie aplikacji na platformie Azure](../app-service/web-sites-scale.md). 

Podczas uruchamiania funkcji JavaScript na plan usługi App Service, należy wybrać plan, który ma mniejszą liczbę procesorów wirtualnych. Aby uzyskać więcej informacji, zobacz [wybierz plany usługi App Service jednordzeniowy](functions-reference-node.md#considerations-for-javascript-functions).  

<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 
<a name="always-on"></a>
### <a name="always-on"></a>Zawsze włączona

Jeśli zostanie uruchomione na plan usługi App Service, należy włączyć **zawsze na** ustawienie, aby aplikacja funkcji zostanie uruchomiona poprawnie. Plan usługi App Service środowisko uruchomieniowe usługi functions zacznie bezczynności, po kilku minutach braku aktywności, dzięki czemu tylko wyzwalaczy HTTP "wzbudzania" funkcji. Zawsze na jest dostępna tylko na plan usługi App Service. W ramach planu zużycie platformy aktywuje automatycznie aplikacji funkcji.

## <a name="what-is-my-hosting-plan"></a>Co to jest Mój plan hostingu

Aby określić plan hostingu używane przez aplikację funkcji, zobacz **planu usługi App Service / warstwa cenowa** w **Przegląd** karty dla aplikacji funkcji w [witryny Azure portal](https://portal.azure.com). Dla planów usługi App Service wskazane jest także warstwy cenowej. 

![Wyświetl plan skalowania w portalu](./media/functions-scale/function-app-overview-portal.png)

Interfejs wiersza polecenia platformy Azure umożliwia również określić plan, w następujący sposób:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Gdy dane wyjściowe tego polecenia jest `dynamic`, aplikacja funkcji znajduje się w planie zużycie. Wszystkie pozostałe wartości wskazują warstw planu usługi App Service.

Nawet w przypadku zawsze na włączone, limit czasu wykonywania poszczególnych funkcji jest kontrolowana przez `functionTimeout` w [host.json](functions-host-json.md#functiontimeout) pliku projektu.

## <a name="storage-account-requirements"></a>Wymagania konta magazynu

Na plan zużycie i plan usługi App Service aplikacja funkcji wymaga konto usługi Azure Storage ogólnego, który obsługuje usługi Azure Blob, kolejki, pliki i Table storage. Jest to spowodowane funkcje opiera się na usłudze Azure Storage dla operacji, takich jak Zarządzanie wyzwalaczami i rejestrowanie wykonań funkcji, ale niektóre konta magazynu nie obsługują kolejek i tabel. Te konta, które obejmują kont usługi storage tylko do obiektów blob (w tym usługi premium storage) i kont magazynu ogólnego przeznaczenia z replikacją magazynu strefowo nadmiarowego, są filtrowane w poziomie z istniejących **konta magazynu** wybrane opcje, po utworzeniu aplikacji funkcji.

<!-- JH: Does using a Premium Storage account improve perf? -->

Aby dowiedzieć się więcej na temat typów kont magazynu, zobacz [wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-plan-works"></a>Jak działa w planie zużycie

W przypadku planu zużycie kontrolera skalowania automatycznie skaluje zasobów Procesora i pamięci przez dodanie dodatkowych wystąpień hosta funkcji, w oparciu o liczbę zdarzeń, które funkcje są wyzwalane na. Każde wystąpienie hosta funkcji jest ograniczona do 1,5 GB pamięci.  Wystąpienie hosta jest aplikacja funkcji, co oznacza wszystkich funkcji w ramach funkcji zasób udziału dla aplikacji w ramach wystąpienia i skalowania, w tym samym czasie. Aplikacje funkcji, które współużytkują ten sam plan zużycie są skalowane niezależnie.  

Korzystając z planu hostingu zużycie, funkcja pliki kodu są przechowywane na udziałów plików platformy Azure na koncie magazynu głównych funkcji. Po usunięciu głównego konta magazynu aplikacji funkcji w plikach kodu funkcji zostaną usunięte i nie można odzyskać.

> [!NOTE]
> Podczas korzystania z wyzwalacz obiektu blob w ramach planu zużycie, może istnieć maksymalnie 10 minut opóźnienia w przetwarzaniu nowe obiekty BLOB. To opóźnienie występuje, gdy aplikacja funkcji przeszedł bezczynności. Po uruchomieniu aplikacji funkcji, obiekty BLOB są przetwarzane od razu. Aby uniknąć tego opóźnienia zimnego startu, użyj planu usługi App Service przy użyciu **Always On** włączona lub użyj wyzwalacza usługi Event Grid. Aby uzyskać więcej informacji, zobacz [artykuł odwołanie do obiektu blob wyzwalacza powiązania](functions-bindings-storage-blob.md#trigger).

### <a name="runtime-scaling"></a>Skalowanie środowiska uruchomieniowego

Usługa Azure Functions używa składnik o nazwie *kontrolera skalowania* monitorować współczynnik zdarzeń i ustalenia, czy należy skalować w poziomie lub w. Kontroler skalowania korzysta z algorytmów heurystycznych dla każdego typu wyzwalacza. Na przykład podczas korzystania z wyzwalacza usługi Azure Queue storage, zostanie przeprowadzone skalowanie na podstawie długości kolejki i wiek najstarsze komunikatu w kolejce.

Jednostka skalowania jest aplikacja funkcji. Gdy aplikacja funkcji jest skalowana w poziomie, dodatkowe zasoby są przydzielane do uruchamiania wielu wystąpień hosta usługi Azure Functions. Z drugiej strony jak moc obliczeniowa, czy żądanie jest krótszy, kontroler skalowania usuwa wystąpień hosta funkcji. Liczba wystąpień jest ostatecznie skalować w dół od zera. gdy żadne funkcje nie są uruchomione w ramach aplikacji funkcji.

![Kontroler skalowania zdarzenia dotyczące monitorowania i tworzenia wystąpień](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Opis zachowania skalowania

Skalowanie może się różnić od szeregu czynników, a inaczej w zależności od języka wybranego i wyzwalaczy skalowania. Istnieją jednak kilka aspektów skalowanie, które istnieją już dziś w systemie:

* Aplikacja funkcji pojedynczej tylko jest skalowany w górę do maksymalnie 200 wystąpień. Pojedyncze wystąpienie może przetwarzać więcej niż jednego komunikatu lub żądania naraz, więc nie ma ustawiony limit na liczbę współbieżnych wykonań.
* Nowe wystąpienia tylko zostaną przydzielone co najwyżej raz na 10 sekund.

Różnymi wyzwalaczami mogą także mieć różne, w ograniczonym zakresie, a także udokumentowanego poniżej:

* [Centrum zdarzeń](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Najlepsze rozwiązania i wzorce skalowalne aplikacje

Istnieje wiele aspektów aplikacji funkcji, które mają wpływ na sposób również zostanie przeprowadzone skalowanie, łącznie z konfiguracją hosta, obciążenie środowiska uruchomieniowego i wydajności zasobów.  Aby uzyskać więcej informacji, zobacz [skalowalności w dalszej części artykułu zagadnienia dotyczące wydajności](functions-best-practices.md#scalability-best-practices). Należy także pamiętać o zachowanie połączenia miarę skalowania aplikacji funkcji. Aby uzyskać więcej informacji, zobacz [sposób zarządzania połączeniami w usłudze Azure Functions](manage-connections.md).

### <a name="billing-model"></a>Model rozliczania

Okres rozliczeniowy za plan zużycie został szczegółowo opisany na [Usługa Azure Functions, cennik]. Sposób użycia są agregowane na poziomie aplikacji funkcji i zlicza czas, który jest wykonywany kod funkcji. Jednostki do rozliczeń są następujące:

* **Użycie zasobów w gigabajtosekundach (GB-s)**. Obliczona jako rozmiar pamięci i czas wykonywania dla wszystkich funkcji w ramach aplikacji funkcji. 
* **Liczba wykonań**. Zliczane każdorazowo, gdy funkcja jest wykonywana w odpowiedzi na wyzwalacz zdarzenia.

[Usługa Azure Functions, cennik]: https://azure.microsoft.com/pricing/details/functions
