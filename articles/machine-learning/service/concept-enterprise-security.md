---
title: Zabezpieczenia przedsiębiorstwa
titleSuffix: Azure Machine Learning service
description: 'Bezpiecznie korzystać z usługi Azure Machine Learning: uwierzytelniania, autoryzacji, zabezpieczeń sieci, szyfrowanie danych i monitorowania.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/10/2019
ms.openlocfilehash: b950e7d38235d089c6236c76136d8ec2fc7a1f74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821348"
---
# <a name="enterprise-security-for-azure-machine-learning-service"></a>Bezpieczeństwo przedsiębiorstwa w usłudze Azure Machine Learning

W tym artykule dowiesz się o funkcjach zabezpieczeń dostępnych przy użyciu usługi Azure usługę Machine learning.

Korzystając z usługi w chmurze, jest najlepszym rozwiązaniem, aby ograniczyć dostęp tylko do użytkowników, którzy go potrzebują. Spowoduje to uruchomienie przez omówienie modelu uwierzytelniania i autoryzacji, używane przez usługę. Możesz również chcieć ograniczyć dostęp do sieci lub bezpiecznie Dołącz do zasobów w sieci lokalnej z tymi w chmurze. Szyfrowanie danych również jest istotna, podczas przechowywania i podczas, gdy dane są przenoszone między usługami. Na koniec musisz mieć możliwość monitorowania usługi i utworzyć dziennik inspekcji wszystkich działań.

## <a name="authentication"></a>Authentication
Multi-Factor authentication jest obsługiwana, jeśli usługi Azure Active Directory (Azure AD) jest skonfigurowany dla tego samego.
* Klient rejestruje się w usłudze Azure AD i pobiera tokenu usługi Azure Resource Manager.  Użytkownicy i nazwy główne usług są w pełni obsługiwane.
* Klient przedstawia token do usługi Azure Resource Manager i wszystkich usług Azure Machine Learning
* Usługa Azure Machine Learning zapewnia tokenu usługi Azure Machine Learning do obliczenia użytkownika. Na przykład obliczeniowego usługi Machine Learning. Tej usługi Azure Machine Learning, w których token jest używany przez użytkownika obliczeń do wywołania zwrotnego do usługi Azure Machine Learning (ogranicza zakres do obszaru roboczego) po uruchomieniu jest pełny.

![Zrzut ekranu pokazujący, jak działa uwierzytelnianie w usłudze Azure Machine Learning](./media/enterprise-readiness/authentication.png)

### <a name="authentication-keys-for-web-service-deployment"></a>Uwierzytelnianie kluczy dla wdrożenia usługi internetowej

Po włączeniu uwierzytelniania dla wdrożenia, możesz automatycznie tworzyć klucze uwierzytelniania.

* Uwierzytelnianie jest włączone domyślnie w przypadku wdrażania usługi Azure Kubernetes Service.
* Uwierzytelnianie jest domyślnie wyłączona w przypadku wdrażania usługi Azure Container Instances.

Aby kontrolować uwierzytelniania, należy użyć `auth_enabled` parametru podczas tworzenia lub aktualizowania wdrożenia.

Jeśli uwierzytelnianie jest włączone, można użyć `get_keys` metodę, aby pobrać klucz podstawowy i pomocniczy uwierzytelniania:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Jeśli konieczne jest ponowne wygenerowanie klucza, należy użyć [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)


## <a name="authorization"></a>Autoryzacja

Możesz utworzyć wiele obszarów roboczych, a każdy obszar roboczy może być współużytkowany przez wiele osób. Po udostępnieniu obszaru roboczego można kontrolować dostęp do niego, przypisując następujące role użytkowników:
* Właściciel
* Współautor
* Czytelnik
    
W poniższej tabeli wymieniono niektóre z podstawowych operacji usługi Azure Machine Learning i role, które mogą wykonywać je:

| Usługa Azure Machine Learning operacji | Właściciel | Współautor | Czytelnik |
| ---- |:----:|:----:|:----:|
| Tworzenie obszaru roboczego | ✓ | ✓ | |
| Udostępnij obszar roboczy | ✓ | |  |
| Create Compute | ✓ | ✓ | |
| Dołącz obliczeń | ✓ | ✓ | |
| Dołącz magazynów danych | ✓ | ✓ | |
| Uruchamianie eksperymentu | ✓ | ✓ | |
| Wyświetlanie uruchomień/metryki | ✓ | ✓ | ✓ |
| Rejestrowanie modelu | ✓ | ✓ | |
| Tworzenie obrazu | ✓ | ✓ | |
| Wdrażanie usługi sieci web | ✓ | ✓ | |
| Przeglądanie modeli/obrazów | ✓ | ✓ | ✓ |
| Wywoływanie usługi sieci web | ✓ | ✓ | ✓ |

Wbudowane role są niewystarczające do własnych potrzeb, można również utworzyć niestandardowe role. Należy zauważyć, że tylko niestandardowe role, które firma Microsoft obsługuje dla operacji na obszar roboczy i obliczeniowego usługi Machine Learning. Role niestandardowe mogą mieć odczytu, zapisu lub usuwania uprawnień obszaru roboczego i zasobu obliczeniowego, w tym obszarze roboczym. Rolę można udostępnić w poziomie określonego obszaru roboczego, na poziomie grupy określonego zasobu lub na poziomie określonej subskrypcji. Aby uzyskać więcej informacji, zobacz [zarządzania użytkownikami i rolami w obszarze roboczym usługi Azure Machine Learning](how-to-assign-roles.md)

### <a name="securing-compute-and-data"></a>Zabezpieczanie moc obliczeniową i dane
Właściciele i współautorzy można użyć wszystkich obliczeniowych elementów docelowych i magazynów danych, które są dołączone do obszaru roboczego.  
Każdy obszar roboczy ma również skojarzonych przypisany systemowo tożsamości zarządzanej (o nazwie identycznej z nazwą obszaru roboczego) z następującymi uprawnieniami na dołączone zasoby używane w obszarze roboczym:

Aby uzyskać więcej informacji na temat zarządzanych tożsamości, zobacz [zarządzanych tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

| Resource | Uprawnienia |
| ----- | ----- |
| Obszar roboczy | Współautor | 
| Konto magazynu | Współautor danych obiektu blob usługi Storage | 
| Usługa Key Vault | Dostęp do wszystkich certyfikatów kluczy, wpisów tajnych oraz ich | 
| Azure Container Registry | Współautor | 
| Grupa zasobów, która zawiera obszar roboczy | Współautor | 
| Grupy zasobów zawierającej usługę Key Vault (jeśli jest inna niż ta, zawierająca obszar roboczy) | Współautor | 

Zaleca się, że administratorzy nie odwołać dostęp zarządzanych tożsamości do zasobów, o których wspomniano powyżej. Dostęp można przywrócić za pomocą operacji ponownej synchronizacji kluczy.

Usługa Azure Machine Learning tworzy dodatkową aplikację (nazwa rozpoczyna się od ciągu aml-) z poziomu dostępu współautora w subskrypcji dla każdego regionu obszaru roboczego. Aby uzyskać przykład. Jeśli masz obszar roboczy w regionie wschodnie stany USA i innego obszaru roboczego w regionie Europa Północna z tą samą subskrypcją, zobaczysz 2 takich aplikacji. Jest to niezbędne, aby zasoby obliczeniowe usługi Azure Machine Learning, usługa może ułatwić zarządzanie.


## <a name="network-security"></a>Bezpieczeństwo sieci

Usługa Azure Machine Learning, zależy od innych usług platformy Azure za zasoby obliczeniowe. Zasoby obliczeniowe (celów obliczeń) są używane do uczenia i wdrażania modeli. Te obliczenia obiekty docelowe mogą być tworzone w sieci wirtualnej. Na przykład można użyć maszyny wirtualnej do nauki o danych firmy Microsoft do nauczenia modelu, a następnie wdrożyć model do usługi Azure Kubernetes Service (AKS).  

Aby uzyskać więcej informacji, zobacz [sposób uruchamiania eksperymentów oraz wnioskowania w sieci wirtualnej](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Szyfrowanie danych

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku
#### <a name="azure-blob-storage"></a>Azure Blob Storage
Usługa Azure Machine Learning są przechowywane migawki, dane wyjściowe i dzienniki na koncie usługi Azure Blob Storage, jest powiązany z obszarem roboczym usługi Azure Machine Learning, która znajduje się w subskrypcji użytkownika. Wszystkie dane, które są przechowywane w magazynie obiektów Blob platformy Azure są szyfrowane w stanie spoczynku przy użyciu kluczy Microsoft-Managed.

Aby uzyskać więcej informacji na temat wyświetlić klucze dla danych przechowywanych w usłudze Azure Blob Storage, zobacz [szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

Szkolenie danych zazwyczaj także są przechowywane w usłudze Azure Blob storage, aby była dostępna dla obliczeń szkolenia. Ten magazyn jest zarządzany przez usługi Azure Machine Learning, ale nie zainstalowane do obliczenia jako w systemie zdalnym plików.

#### <a name="cosmos-db"></a>Cosmos DB
Usługa Azure Machine Learning przechowuje metryki i metadane usługi Cosmos DB, który znajduje się w ramach subskrypcji Microsoft zarządzane przez usługę Azure Machine Learning. Wszystkie dane, które są przechowywane w usłudze Cosmos DB są szyfrowane w stanie spoczynku przy użyciu kluczy zarządzanych firmy Microsoft.

#### <a name="azure-container-registry-acr"></a>Usługa Azure Container Registry (ACR)
Wszystkie obrazy kontenerów w rejestrze (ACR) są szyfrowane, gdy. Platforma Azure automatycznie szyfruje obraz przed przekazaniem jej i odszyfrowuje je na bieżąco, gdy usługa Azure Machine Learning ściąga obraz.

#### <a name="machine-learning-compute"></a>Środowisko obliczeniowe usługi Machine Learning
Dysk systemu operacyjnego na każdym węźle obliczeniowym jest przechowywany w usłudze Azure Storage są szyfrowane przy użyciu kluczy zarządzanych firmy Microsoft w ramach kont magazynu usługi Azure Machine Learning. To obliczenie jest tymczasowych i klastrów zwykle są skalowane w dół, gdy brak uruchomień w kolejce. Podstawowej maszyny wirtualnej jest rozliczeniu i usunąć dysk systemu operacyjnego. Usługa Azure disk encryption nie jest obsługiwane dla dysku systemu operacyjnego.
Każda maszyna wirtualna ma również lokalnego dysku tymczasowego dla operacji systemu operacyjnego. Ten dysk można również opcjonalnie używany do przygotowania danych szkoleniowych. Ten dysk nie jest zaszyfrowany. Aby uzyskać więcej informacji o sposobie działania szyfrowanie danych magazynowanych na platformie Azure, zobacz [danych na platformę Azure szyfrowania podczas spoczynku](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest). 

### <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu
Obsługiwane są zarówno komunikację wewnętrzną między różnych mikro usługi Azure Machine Learning i zewnętrznej komunikacji podczas wywoływania punktu końcowego oceniania przy użyciu protokołu SSL. Wszelki dostęp do usługi Azure Storage jest również za pośrednictwem bezpiecznego kanału. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie usługi Azure Machine Learning usług sieci web przy użyciu protokołu SSL](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Za pomocą usługi Azure Key Vault
Wystąpienia usługi Key Vault skojarzone z obszarem roboczym jest używany przez usługę Azure Machine Learning do przechowywania poświadczeń różnych rodzajów:
* Parametry połączenia konta magazynu skojarzone
* Hasła do wystąpień repozytorium kontenerów platformy Azure
* Połączenie z danymi ciągi magazynów. 

Hasła SSH i klucze do obliczenia elementów docelowych, takich jak HDInsight usługi HDI i maszyny Wirtualnej są przechowywane w oddzielnych usługi Key Vault, który jest skojarzony z subskrypcją Microsoft Azure. Usługa Azure Machine Learning przechowywania hasła lub kluczy podanego przez użytkownika zamiast go generuje autoryzuje i przechowuje swoje własne klucze SSH, aby można było nawiązać połączenie z maszyną Wirtualną/HDInsight do uruchamiania eksperymentów. Każdy obszar roboczy ma skojarzone przypisany systemowo zarządzana tożsamość (o nazwie identycznej z nazwą obszaru roboczego) mającego dostęp do kluczy, wpisów tajnych i certyfikatów w usłudze Key Vault.

 
## <a name="monitoring"></a>Monitorowanie

Użytkownicy widzą dziennika aktywności w obszarze roboczym, aby zobaczyć różne operacje wykonywane w obszarze roboczym i uzyskać podstawowe informacje, takie jak nazwa operacji, zdarzenie zainicjowane przez sygnatura czasowa itp.

Poniższy zrzut ekranu przedstawia dziennika aktywności dla obszaru roboczego:

![Zrzut ekranu przedstawiający działanie dziennika w obszarze roboczym](./media/enterprise-readiness/workspace-activity-log.png)


Szczegóły wyników żądania są przechowywane w usłudze AppInsights, który jest tworzony w ramach subskrypcji użytkownika podczas tworzenia obszaru roboczego. W tym pól, takich jak HTTPMethod UserAgent, ComputeType, RequestUrl, StatusCode, RequestId, czas trwania itp.


## <a name="data-flow-diagram"></a>Diagram przepływu danych

### <a name="create-workspace"></a>Tworzenie obszaru roboczego
Na poniższym diagramie przedstawiono przepływ pracy tworzenia obszaru roboczego.
Użytkownik loguje się do usługi Azure AD za pomocą dowolnego z obsługiwanych klientów usługi Azure Machine Learning (portal Azure CLI, w przypadku zestawu SDK języka Python) i żąda odpowiedniego tokenu usługi Azure Resource Manager.  Użytkownik wywołuje usługi Azure Resource Manager do utworzenia obszaru roboczego.  Usługa Azure Resource Manager kontakty usługi Azure Machine Learning usługi dostawcy zasobów można aprowizować obszaru roboczego.  Dodatkowe zasoby są tworzone w ramach subskrypcji klienta podczas tworzenia obszaru roboczego:
* Magazyn kluczy (na potrzeby przechowywania wpisów tajnych)
* Konto usługi Azure Storage (w tym obiektów Blob i udział plików)
* Usługa Azure Container Registry (w celu przechowywania obrazów platformy docker do wnioskowania i eksperymentowania)
* Usługa Application Insights (na potrzeby przechowywania danych telemetrycznych)

Inne usługi obliczeniowe dołączone do obszaru roboczego (usługi Azure Kubernetes Service, maszyna wirtualna itd.) mogą również udostępniane przez klientów, zgodnie z potrzebami. 

![Zrzut ekranu przedstawiający tworzenie przepływu pracy dla obszaru roboczego](./media/enterprise-readiness/create-workspace.png)

### <a name="save-source-code-training-scripts"></a>Zapisz kod źródłowy (skryptów szkolenia)
Na poniższym diagramie przedstawiono przepływ pracy migawek kodu.
Skojarzone z usługi Azure Machine Learning obszaru roboczego usługi są katalogów (eksperymenty) zawierającego kod źródłowy (skrypty szkoleń).  Są one przechowywane na komputerze lokalnym przez klienta, jak i w chmurze (w magazynie obiektów Blob platformy Azure w ramach subskrypcji klienta). Te migawki kodu są używane do wykonywania lub inspekcji do przeprowadzania inspekcji historycznych.

![Zrzut ekranu przedstawiający tworzenie przepływu pracy dla obszaru roboczego](./media/enterprise-readiness/code-snapshot.png)

### <a name="training"></a>Szkolenia
Na poniższym diagramie przedstawiono przepływ pracy szkolenia.
* Usługa Azure Machine Learning jest wywoływana przy użyciu Identyfikatora migawki dla migawki kod zapisany wcześniej
* Usługa Azure Machine Learning, który tworzy usługi Uruchom (opcjonalnie) identyfikator & tokenem usługi Azure Machine Learning, który jest później używany przez celów obliczeń, takich jak Machine obliczeń/maszyna wirtualna do uczenia odpowiedzieć usługi Azure Machine Learning
* Można wybrać obu zarządzane zasoby obliczeniowe (np.) Usługi Machine Learning obliczeń) lub niezarządzanego obliczeniowe (np.) Maszyna wirtualna) do uruchamiania zadań szkoleniowych. Przepływ danych zostało wyjaśnione w obu scenariuszach poniżej:
* (Maszyny Wirtualnej/HDInsight/elementu lokalnego — uzyskiwać dostęp za pomocą poświadczenia protokołu SSH w usłudze Key Vault w ramach subskrypcji firmy Microsoft) Usługa Azure Machine Learning działa kod zarządzania na cel obliczenia który:
    1.  Przygotowanie środowiska (Uwaga: Docker to także opcję dla maszyny Wirtualnej i lokalnym. Zobacz kroki obliczeniowego usługi Machine Learning poniżej zrozumieć, jak uruchamianie eksperymentu dotyczące działania kontenera platformy docker)
    2.  Pobiera kod
    3.  Konfiguruje środowisko zmienne/konfiguracji
    4.  Uruchamia skrypt użytkownika (migawka kodu wymienionymi powyżej)
* (Machine Learning obliczenia — uzyskiwać dostęp za pomocą tożsamości zarządzanych obszaru roboczego) Należy pamiętać, że od czasu obliczeniowego usługi Machine Learning jest zarządzane zasoby obliczeniowe oznacza to, że jest ona zarządzana przez firmę Microsoft w wyniku działa w ramach subskrypcji Microsoft.
    1.  Zdalnego konstrukcji Docker rozpocznie się kompilowanie, jeśli to konieczne
    2.  Pisze kod, zarządzanie użytkownikiem udział plików platformy Azure
    3.  Uruchamia kontener za pomocą początkowego polecenie oznacza to, że kod zarządzania w kroku powyżej


#### <a name="querying-runs--metrics"></a>Zapytania uruchomień i metryk
Ten krok jest wyświetlany w usłudze flow, gdzie szkolenia obliczenia zapisów *Uruchom metryki* do usługi Azure Machine Learning, z których pobiera przechowywane w usłudze Cosmos DB. Klientów można wywołać usługi Azure Machine Learning, która z kolei zostanie pobrana metryki z usługi Cosmos DB i przywrócić go z powrotem do klienta.

![Zrzut ekranu przedstawiający tworzenie przepływu pracy dla obszaru roboczego](./media/enterprise-readiness/training-and-metrics.png)

### <a name="creating-web-services"></a>Tworzenie usługi sieci web
Na poniższym diagramie przedstawiono przepływ pracy wnioskowania, w której model jest wdrażany jako usługę sieci web.
Zobacz szczegóły poniżej:
* Użytkownik rejestruje modelu przy użyciu klienta, takich jak zestaw SDK usługi Azure ML
* Użytkownik tworzy obraz przy użyciu modelu, wynik plików i inne zależności modelu
* Obraz platformy Docker jest tworzony i zapisywany w rejestru Azure container Registry
* Usługa sieci Web jest wdrażana na docelowym obliczeń (ACI/AKS), korzystając z obrazu utworzonego powyżej
* Oceniania szczegóły żądania są przechowywane w usłudze AppInsights, który znajduje się w subskrypcji użytkownika
* Dane telemetryczne są również wypychane do subskrypcji platformy Microsoft/Azure

![Zrzut ekranu przedstawiający tworzenie przepływu pracy dla obszaru roboczego](./media/enterprise-readiness/inferencing.png)

## <a name="next-steps"></a>Kolejne kroki

* [Zabezpieczania usług sieci web Azure Machine Learning przy użyciu protokołu SSL](how-to-secure-web-service.md)
* [Korzystanie z modelu uczenia Maszynowego, wdrożyć jako usługę sieci web](how-to-consume-web-service.md)
* [Jak uruchomić prognoz usługi batch](how-to-run-batch-predictions.md)
* [Monitoruj swoje modele usługi Azure Machine Learning z usługą Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
* [Usługa Azure Machine Learning zestawu SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Usługa Azure Machine Learning za pomocą usługi Azure Virtual Networks](how-to-enable-virtual-network.md)
* [Najlepsze rozwiązania dotyczące tworzenia systemów rekomendacji](https://github.com/Microsoft/Recommenders)
* [Tworzenie interfejsu API zaleceń w czasie rzeczywistym na platformie Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
