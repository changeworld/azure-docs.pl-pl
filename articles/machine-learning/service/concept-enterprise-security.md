---
title: Zabezpieczenia przedsiębiorstwa
titleSuffix: Azure Machine Learning service
description: 'Bezpiecznie Używaj usługi Azure Machine Learning: uwierzytelnianie, autoryzacja, zabezpieczenia sieci, szyfrowanie danych i monitorowanie.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/07/2019
ms.openlocfilehash: 81e8601ac83d43bde0767e38eb387f489d76125b
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165242"
---
# <a name="enterprise-security-for-the-azure-machine-learning-service"></a>Zabezpieczenia przedsiębiorstwa dla usługi Azure Machine Learning

Ten artykuł zawiera informacje o funkcjach zabezpieczeń dostępnych dla usługi Azure Machine Learning.

W przypadku korzystania z usługi w chmurze najlepszym rozwiązaniem jest ograniczenie dostępu tylko do użytkowników, którzy ich potrzebują. Zacznij od ustalenia modelu uwierzytelniania i autoryzacji używanego przez usługę. Możesz również ograniczyć dostęp do sieci lub bezpiecznie dołączać zasoby w sieci lokalnej do chmury. Szyfrowanie danych jest również niezbędne, zarówno w czasie spoczynku, jak i podczas przenoszenia danych między usługami. Na koniec należy mieć możliwość monitorowania usługi i tworzenia dziennika inspekcji dla wszystkich działań.

## <a name="authentication"></a>Authentication

Uwierzytelnianie wieloskładnikowe jest obsługiwane, jeśli Azure Active Directory (Azure AD) jest skonfigurowany do korzystania z niego. Oto proces uwierzytelniania:

1. Klient loguje się do usługi Azure AD i pobiera token Azure Resource Manager.  Nazwy główne użytkowników i usług są w pełni obsługiwane.
1. Klient przedstawia token, aby Azure Resource Manager i wszystkie usługi Azure Machine Learning.
1. Usługa Machine Learning udostępnia token usługi Machine Learning dla elementu docelowego obliczeń użytkownika (na przykład środowisko obliczeniowe usługi Machine Learning). Ten token jest używany przez element docelowy obliczeń użytkownika do wywołania zwrotnego do usługi Machine Learning po zakończeniu przebiegu. Zakres jest ograniczony do obszaru roboczego.

[![Uwierzytelnianie w usłudze Azure Machine Learning](./media/enterprise-readiness/authentication.png)](./media/enterprise-readiness/authentication-expanded.png)

### <a name="authentication-for-web-service-deployment"></a>Uwierzytelnianie dla wdrożenia usługi sieci Web

Azure Machine Learning obsługuje dwie formy uwierzytelniania dla usług sieci Web: klucz i token. Każda usługa sieci Web może jednocześnie włączyć tylko jedną formę uwierzytelniania.

|Metoda uwierzytelniania|Azure Container Instances|AKS|
|---|---|---|
|Klucz|Domyślnie wyłączone| Włączona domyślnie|
|Token| Niedostępny| Domyślnie wyłączone |

#### <a name="authentication-with-keys"></a>Uwierzytelnianie przy użyciu kluczy

Po włączeniu uwierzytelniania klucza dla wdrożenia automatycznie tworzone są klucze uwierzytelniania.

* Uwierzytelnianie jest domyślnie włączone w przypadku wdrażania w usłudze Azure Kubernetes Service (AKS).
* Podczas wdrażania programu do Azure Container Instances uwierzytelnianie jest domyślnie wyłączone.

Aby włączyć uwierzytelnianie klucza, użyj `auth_enabled` parametru podczas tworzenia lub aktualizowania wdrożenia.

Jeśli uwierzytelnianie klucza jest włączone, można użyć `get_keys` metody, aby pobrać podstawowy i pomocniczy klucz uwierzytelniania:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Jeśli konieczne jest ponowne wygenerowanie klucza, użyj [ `service.regen_key` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

#### <a name="authentication-with-tokens"></a>Uwierzytelnianie przy użyciu tokenów

Po włączeniu uwierzytelniania tokenów dla usługi sieci Web użytkownicy muszą przedstawić Azure Machine Learning token sieci Web JSON dla usługi sieci Web, aby uzyskać do niej dostęp.

* Uwierzytelnianie tokenu jest domyślnie wyłączone w przypadku wdrażania w usłudze Azure Kubernetes Service.
* Uwierzytelnianie tokenu nie jest obsługiwane w przypadku wdrażania programu w celu Azure Container Instances.

Aby kontrolować uwierzytelnianie tokenu, użyj `token_auth_enabled` parametru podczas tworzenia lub aktualizowania wdrożenia.

Jeśli jest włączone uwierzytelnianie tokenu, można użyć `get_token` metody, aby pobrać token sieci Web JSON (JWT) i czas wygaśnięcia tego tokenu:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Po upływie `refresh_by` czasu tokenu musisz zażądać nowego tokenu.
>
> Zdecydowanie zalecamy utworzenie obszaru roboczego Azure Machine Learning w tym samym regionie, w którym znajduje się klaster usługi Azure Kubernetes. 
>
> W celu uwierzytelnienia przy użyciu tokenu usługa sieci Web wykona wywołanie do regionu, w którym jest tworzony obszar roboczy Azure Machine Learning. Jeśli region obszaru roboczego jest niedostępny, nie będzie można pobrać tokenu dla usługi sieci Web, nawet jeśli klaster znajduje się w innym regionie niż obszar roboczy. W efekcie uwierzytelnianie usługi Azure AD jest niedostępne, dopóki region obszaru roboczego nie będzie dostępny ponownie. 
>
> Ponadto im większa odległość między regionem klastra a regionem obszaru roboczego, tym dłużej potrwa pobieranie tokenu.

## <a name="authorization"></a>Authorization

Możesz utworzyć wiele obszarów roboczych, a każdy obszar roboczy może być współużytkowany przez wiele osób. Po udostępnieniu obszaru roboczego można kontrolować do niego dostęp, przypisując te role do użytkowników:

* Właściciel
* Współautor
* Czytelnik

W poniższej tabeli wymieniono niektóre główne operacje usługi Azure Machine Learning i role, które mogą je wykonać:

| Azure Machine Learning operacji usługi | Właściciel | Współautor | Czytelnik |
| ---- |:----:|:----:|:----:|
| Tworzenie obszaru roboczego | ✓ | ✓ | |
| Udostępnianie obszaru roboczego | ✓ | |  |
| Utwórz element docelowy obliczeń | ✓ | ✓ | |
| Dołącz cel obliczeń | ✓ | ✓ | |
| Dołącz magazyny danych | ✓ | ✓ | |
| Uruchamianie eksperymentu | ✓ | ✓ | |
| Wyświetl przebiegi/metryki | ✓ | ✓ | ✓ |
| Rejestrowanie modelu | ✓ | ✓ | |
| Tworzenie obrazu | ✓ | ✓ | |
| Wdróż usługę sieci Web | ✓ | ✓ | |
| Wyświetlanie modeli/obrazów | ✓ | ✓ | ✓ |
| Wywoływanie usługi sieci Web | ✓ | ✓ | ✓ |

Jeśli wbudowane role nie spełniają Twoich potrzeb, można utworzyć role niestandardowe. Role niestandardowe są obsługiwane tylko w przypadku operacji w obszarze roboczym i środowisko obliczeniowe usługi Machine Learning. Role niestandardowe mogą mieć uprawnienia do odczytu, zapisu lub usuwania w obszarze roboczym i w zasobie obliczeniowym w tym obszarze roboczym. Rolę można udostępnić na określonym poziomie obszaru roboczego, na określonym poziomie grupy zasobów lub na określonym poziomie subskrypcji. Aby uzyskać więcej informacji, zobacz [Zarządzanie użytkownikami i rolami w obszarze roboczym Azure Machine Learning](how-to-assign-roles.md).

### <a name="securing-compute-targets-and-data"></a>Zabezpieczanie obiektów docelowych obliczeń i danych

Właściciele i Współautorzy mogą używać wszystkich obiektów docelowych obliczeń i magazynów danych, które są dołączone do obszaru roboczego.  

Każdy obszar roboczy ma także skojarzoną tożsamość zarządzaną przypisaną przez system, która ma taką samą nazwę jak obszar roboczy. Zarządzana tożsamość ma następujące uprawnienia dotyczące dołączonych zasobów używanych w obszarze roboczym.

Aby uzyskać więcej informacji o tożsamościach zarządzanych, zobacz [zarządzane tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

| Resource | Uprawnienia |
| ----- | ----- |
| Obszar roboczy | Współautor |
| Konto magazynu | Współautor danych obiektu blob usługi Storage |
| Magazyn kluczy | Dostęp do wszystkich kluczy, wpisów tajnych, certyfikatów |
| Rejestr kontenerów platformy Azure | Współautor |
| Grupa zasobów, która zawiera obszar roboczy | Współautor |
| Grupa zasobów zawierająca Magazyn kluczy (jeśli jest inna niż ta, która zawiera obszar roboczy) | Współautor |

Nie zaleca się, aby administratorzy mogli odwołać dostęp do tożsamości zarządzanej do zasobów wymienionych w powyższej tabeli. Dostęp można przywrócić przy użyciu operacji ponowna synchronizacja kluczy.

Usługa Azure Machine Learning tworzy dodatkową aplikację (nazwa rozpoczyna się od `aml-`) z dostępem na poziomie współautora w ramach subskrypcji dla każdego regionu obszaru roboczego. Jeśli na przykład masz jeden obszar roboczy w regionie Wschodnie stany USA i inny obszar roboczy w Europie Północnej w tej samej subskrypcji, zobaczysz dwie z tych aplikacji. Te aplikacje umożliwiają usłudze Azure Machine Learning, która ułatwia zarządzanie zasobami obliczeniowymi.

## <a name="network-security"></a>Bezpieczeństwo sieci

Usługa Azure Machine Learning opiera się na innych usługach platformy Azure dla zasobów obliczeniowych. Zasoby obliczeniowe (cele obliczeniowe) są używane do uczenia i wdrażania modeli. Te obiekty docelowe obliczeń można utworzyć w sieci wirtualnej. Na przykład możesz użyć usługi Azure Data Science Virtual Machine, aby szkolić model, a następnie wdrożyć model w AKS.  

Aby uzyskać więcej informacji, zobacz [jak uruchamiać eksperymenty i wnioskowania w sieci wirtualnej](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Szyfrowanie danych

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Usługa Azure Machine Learning przechowuje migawki, dane wyjściowe i dzienniki na koncie magazynu obiektów blob platformy Azure, które jest powiązane z obszarem roboczym usługi Azure Machine Learning i subskrypcją. Wszystkie dane przechowywane w usłudze Azure Blob Storage są szyfrowane w stanie spoczynku przy użyciu kluczy zarządzanych przez firmę Microsoft.

Aby uzyskać informacje na temat korzystania z własnych kluczy dla danych przechowywanych w usłudze Azure Blob Storage, zobacz [szyfrowanie usługi Azure Storage z kluczami zarządzanymi przez klienta w Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

Dane szkoleniowe są zazwyczaj przechowywane w usłudze Azure Blob Storage, dzięki czemu są dostępne do uczenia celów obliczeniowych. Ten magazyn nie jest zarządzany przez Azure Machine Learning ale jest instalowany do celów obliczeniowych jako zdalny system plików.

Aby uzyskać informacje o ponownym generowaniu kluczy dostępu dla kont usługi Azure Storage używanych z Twoim obszarem roboczym, zobacz Ponowne [generowanie kluczy dostępu do magazynu](how-to-change-storage-access-key.md).

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Usługa Azure Machine Learning przechowuje metryki i metadane w wystąpieniu Azure Cosmos DB skojarzonym z subskrypcją firmy Microsoft zarządzaną przez usługę Azure Machine Learning. Wszystkie dane przechowywane w Azure Cosmos DB są szyfrowane w stanie spoczynku przy użyciu kluczy zarządzanych przez firmę Microsoft.

#### <a name="azure-container-registry"></a>Rejestr kontenerów platformy Azure

Wszystkie obrazy kontenerów w rejestrze (Azure Container Registry) są szyfrowane w stanie spoczynku. Platforma Azure automatycznie szyfruje obraz przed jego zapisaniem i odszyfrowuje go na bieżąco, gdy usługa Azure Machine Learning pobiera obraz.

#### <a name="machine-learning-compute"></a>Środowisko obliczeniowe usługi Machine Learning

Dysk systemu operacyjnego dla każdego węzła obliczeniowego przechowywanego w usłudze Azure Storage jest szyfrowany przy użyciu kluczy zarządzanych przez firmę Microsoft w ramach kont magazynu usługi Azure Machine Learning. Ten obiekt docelowy obliczeń jest nieulotny, a klastry są zwykle skalowane w dół, gdy żadne przebiegi nie są umieszczane w kolejce. Podstawowa maszyna wirtualna jest nieobsługiwana i dysk systemu operacyjnego zostanie usunięty. Azure Disk Encryption nie jest obsługiwana w przypadku dysku systemu operacyjnego.

Każda maszyna wirtualna ma także lokalny dysk tymczasowy dla operacji systemu operacyjnego. Jeśli chcesz, możesz użyć dysku do przygotowania danych szkoleniowych. Dysk nie jest szyfrowany.
Aby uzyskać więcej informacji na temat sposobu, w jaki szyfrowanie w spoczynku działa na platformie Azure, zobacz [szyfrowanie danych platformy Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest).

### <a name="encryption-in-transit"></a>Szyfrowanie podczas przesyłania

Za pomocą protokołu SSL można zabezpieczyć wewnętrzną komunikację między Azure Machine Learning mikrousługami i zabezpieczyć wywołania zewnętrzne do punktu końcowego oceniania. Cały dostęp do usługi Azure Storage odbywa się również za pośrednictwem bezpiecznego kanału.

Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSL w celu zabezpieczenia usługi sieci Web za pośrednictwem Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Używanie Azure Key Vault

Usługa Azure Machine Learning używa wystąpienia Azure Key Vault skojarzonego z obszarem roboczym do przechowywania poświadczeń różnych rodzajów:

* Powiązane parametry połączenia konta magazynu
* Hasła do wystąpień repozytorium kontenerów platformy Azure
* Parametry połączenia do magazynów danych

Hasła i klucze SSH do obliczeń docelowych, takich jak usługa Azure HDInsight i maszyny wirtualne, są przechowywane w osobnym magazynie kluczy skojarzonym z subskrypcją firmy Microsoft. W usłudze Azure Machine Learning nie są przechowywane żadne hasła ani klucze udostępniane przez użytkowników. Zamiast tego generuje, autoryzuje i przechowuje własne klucze SSH do łączenia się z maszynami wirtualnymi i HDInsight w celu uruchamiania eksperymentów.

Każdy obszar roboczy ma skojarzoną w systemie tożsamość zarządzaną, która ma taką samą nazwę jak obszar roboczy. Ta tożsamość zarządzana ma dostęp do wszystkich kluczy, wpisów tajnych i certyfikatów w magazynie kluczy.

## <a name="monitoring"></a>Monitorowanie

### <a name="metrics"></a>Metryki

Metryki Azure Monitor umożliwiają wyświetlanie i monitorowanie metryk dla obszaru roboczego usługi Azure Machine Learning. W [Azure Portal](https://portal.azure.com)wybierz swój obszar roboczy, a następnie wybierzpozycję metryki:

[![Zrzut ekranu przedstawiający przykładowe metryki dla obszaru roboczego](./media/enterprise-readiness/workspace-metrics.png)](./media/enterprise-readiness/workspace-metrics-expanded.png)

Metryki obejmują informacje dotyczące uruchamiania, wdrożeń i rejestracji.

Aby uzyskać więcej informacji, zobacz [metryki w Azure monitor](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Dziennik aktywności

Możesz wyświetlić dziennik aktywności obszaru roboczego, aby zobaczyć różne operacje wykonywane w obszarze roboczym. Dziennik zawiera podstawowe informacje, takie jak nazwa operacji, inicjator zdarzenia i sygnatura czasowa.

Ten zrzut ekranu przedstawia dziennik aktywności obszaru roboczego:

[![Zrzut ekranu przedstawiający dziennik aktywności obszaru roboczego](./media/enterprise-readiness/workspace-activity-log.png)](./media/enterprise-readiness/workspace-activity-log-expanded.png)

Szczegóły żądania oceniania są przechowywane w Application Insights. Application Insights jest tworzony w ramach subskrypcji podczas tworzenia obszaru roboczego. Zarejestrowane informacje zawierają pola, takie jak HTTPMethod, UserAgent, Computetype, RequestUrl, StatusCode, Numer_id_żądania i Duration.

> [!IMPORTANT]
> Niektóre akcje w obszarze roboczym Azure Machine Learning nie rejestrują informacji w dzienniku aktywności. Na przykład rozpoczęcie przebiegu szkoleniowego i rejestracja modelu nie są rejestrowane.
>
> Niektóre z tych akcji są wyświetlane w obszarze **działania** obszaru roboczego, ale te powiadomienia nie wskazują, kto zainicjował działanie.

## <a name="data-flow-diagrams"></a>Diagramy przepływu danych

### <a name="create-workspace"></a>Tworzenie obszaru roboczego

Na poniższym diagramie przedstawiono przepływ pracy tworzenia obszaru roboczego.

* Użytkownik loguje się do usługi Azure AD z jednego z obsługiwanych klientów usługi Azure Machine Learning (interfejs wiersza polecenia platformy Azure, zestaw SDK języka Python, Azure Portal) i żąda odpowiedniego tokenu Azure Resource Manager.
* Użytkownik wywołuje Azure Resource Manager, aby utworzyć obszar roboczy. 
* Azure Resource Manager skontaktować się z dostawcą zasobów usługi Azure Machine Learning w celu udostępnienia obszaru roboczego.

Dodatkowe zasoby są tworzone w ramach subskrypcji użytkownika podczas tworzenia obszaru roboczego:

* Key Vault (do przechowywania wpisów tajnych)
* Konto usługi Azure Storage (w tym obiekty blob i udziały plików)
* Azure Container Registry (do przechowywania obrazów platformy Docker na potrzeby wnioskowania/oceny i eksperymentowania)
* Application Insights (do przechowywania danych telemetrycznych)

Użytkownik może również udostępnić innym obiektom docelowym obliczeń, które są dołączone do obszaru roboczego (np. usługi Azure Kubernetes lub maszyn wirtualnych).

[![Przepływ pracy tworzenia obszaru roboczego](./media/enterprise-readiness/create-workspace.png)](./media/enterprise-readiness/create-workspace-expanded.png)

### <a name="save-source-code-training-scripts"></a>Zapisz kod źródłowy (skrypty szkoleniowe)

Na poniższym diagramie przedstawiono przepływ pracy migawek kodu.

Skojarzona z obszarem roboczym usługi Azure Machine Learning są katalogami (eksperymenty), które zawierają kod źródłowy (skrypty szkoleniowe). Te skrypty są przechowywane na komputerze lokalnym i w chmurze (w usłudze Azure Blob Storage w ramach subskrypcji). Migawki kodu są używane do wykonywania lub inspekcji inspekcji historycznej.

[![Przepływ pracy migawek kodu](./media/enterprise-readiness/code-snapshot.png)](./media/enterprise-readiness/code-snapshot-expanded.png)

### <a name="training"></a>Szkolenia

Na poniższym diagramie przedstawiono przepływ pracy szkoleniowej.

* Usługa Azure Machine Learning jest wywoływana z IDENTYFIKATORem migawki dla migawki kodu zapisanej w poprzedniej sekcji.
* Usługa Azure Machine Learning tworzy identyfikator uruchomienia (opcjonalnie) i token usługi Machine Learning, który jest później używany przez cele obliczeniowe, takie jak środowisko obliczeniowe usługi Machine Learning/VM, do komunikowania się z usługą Machine Learning.
* Aby uruchamiać zadania szkoleniowe, można wybrać zarządzany obiekt docelowy obliczeń (na przykład środowisko obliczeniowe usługi Machine Learning) lub niezarządzany obiekt docelowy obliczeń (na przykład maszyny wirtualne). Poniżej przedstawiono przepływy danych dla obu scenariuszy:
   * Maszyny wirtualne/HDInsight, do których dostęp odbywa się przy użyciu poświadczeń SSH w magazynie kluczy w ramach subskrypcji firmy Microsoft. Usługa Azure Machine Learning uruchamia kod zarządzania w obiekcie docelowym obliczeń, który:

   1. Przygotowuje środowisko. (Docker to opcja dla maszyn wirtualnych i komputerów lokalnych. Aby dowiedzieć się, jak działają eksperymenty w kontenerach platformy Docker, zobacz następujące środowisko obliczeniowe usługi Machine Learning kroki.
   1. Pobiera kod.
   1. Konfiguruje zmienne środowiskowe i konfiguracje.
   1. Uruchamia skrypty użytkownika (migawka kodu wymieniona w poprzedniej sekcji).

   * Środowisko obliczeniowe usługi Machine Learning dostęp do programu za pomocą tożsamości zarządzanej przez obszar roboczy.
Ponieważ środowisko obliczeniowe usługi Machine Learning jest zarządzanym elementem docelowym obliczeń (czyli jest zarządzany przez firmę Microsoft), jest uruchamiany w ramach Twojej subskrypcji firmy Microsoft.

   1. Zdalna konstrukcja platformy Docker jest wyłączona, w razie konieczności.
   1. Kod zarządzania jest zapisywana w udziale Azure Files użytkownika.
   1. Kontener jest uruchamiany przy użyciu polecenia początkowego. Oznacza to, że kod zarządzania zgodnie z opisem w poprzednim kroku.

#### <a name="querying-runs-and-metrics"></a>Wykonywanie zapytań dotyczących przebiegów i metryk

Na poniższym diagramie przepływu ten krok występuje, gdy obiekt docelowy obliczeń szkolenia zapisuje metryki uruchamiania z powrotem do usługi Azure Machine Learning z magazynu w bazie danych Cosmos DB. Klienci mogą wywoływać usługę Azure Machine Learning. Machine Learning spowoduje włączenie metryk ściągania z bazy danych Cosmos DB i zwrócenie ich z powrotem do klienta.

[![Przepływ pracy szkolenia](./media/enterprise-readiness/training-and-metrics.png)](./media/enterprise-readiness/training-and-metrics-expanded.png)

### <a name="creating-web-services"></a>Tworzenie usług sieci Web

Na poniższym diagramie przedstawiono przepływ pracy wnioskowania. Wnioskowanie lub ocenianie modelu to faza, w której wdrożony model jest używany do prognozowania, najczęściej na danych produkcyjnych.

Oto szczegółowe informacje:

* Użytkownik rejestruje model przy użyciu klienta, takiego jak zestaw Azure Machine Learning SDK.
* Użytkownik tworzy obraz przy użyciu modelu, pliku wynikowego i innych zależności modelu.
* Obraz platformy Docker jest tworzony i przechowywany w Azure Container Registry.
* Usługa sieci Web jest wdrażana w obiekcie docelowym obliczeń (Container Instances/AKS) przy użyciu obrazu utworzonego w poprzednim kroku.
* Szczegóły żądania oceniania są przechowywane w Application Insights, które znajdują się w subskrypcji użytkownika.
* Dane telemetryczne są również wypychane do subskrypcji Microsoft/Azure.

[![Przepływ pracy wnioskowania](./media/enterprise-readiness/inferencing.png)](./media/enterprise-readiness/inferencing-expanded.png)

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczania usług sieci web Azure Machine Learning przy użyciu protokołu SSL](how-to-secure-web-service.md)
* [Korzystanie z modelu Machine Learning wdrożonego jako usługa sieci Web](how-to-consume-web-service.md)
* [Jak uruchomić prognoz usługi batch](how-to-run-batch-predictions.md)
* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
* [Zestaw SDK usługi Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Korzystanie z usługi Azure Machine Learning z usługą Azure Virtual Network](how-to-enable-virtual-network.md)
* [Najlepsze rozwiązania dotyczące kompilowania systemów rekomendacji](https://github.com/Microsoft/Recommenders)
* [Kompilowanie interfejsu API rekomendacji w czasie rzeczywistym na platformie Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
