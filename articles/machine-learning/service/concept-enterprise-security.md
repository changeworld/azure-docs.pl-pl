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
ms.openlocfilehash: 510f58cc0b71fb75ac6f5e15fc883c3caf4a8f9a
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69898026"
---
# <a name="enterprise-security-for-azure-machine-learning-service"></a>Zabezpieczenia przedsiębiorstwa dla usługi Azure Machine Learning

W tym artykule przedstawiono informacje o funkcjach zabezpieczeń dostępnych w usłudze Azure Machine Learning.

W przypadku korzystania z usługi w chmurze najlepszym rozwiązaniem jest ograniczenie dostępu tylko do użytkowników, którzy ich potrzebują. Ten proces rozpocznie się, opisując model uwierzytelniania i autoryzacji używany przez usługę. Możesz również ograniczyć dostęp do sieci lub bezpiecznie dołączać zasoby w sieci lokalnej do chmury. Szyfrowanie danych jest również niezbędne, zarówno w czasie spoczynku, jak i podczas przenoszenia danych między usługami. Na koniec należy mieć możliwość monitorowania usługi i tworzenia dziennika inspekcji dla wszystkich działań.

## <a name="authentication"></a>Authentication

Uwierzytelnianie wieloskładnikowe jest obsługiwane, jeśli Azure Active Directory (Azure AD) jest skonfigurowany dla tego samego.

* Klient loguje się do usługi Azure AD i pobiera Azure Resource Manager token.  Nazwy główne użytkowników i usług są w pełni obsługiwane.
* Klient przedstawia token, aby Azure Resource Manager & wszystkie usługi Azure Machine Learning
* Usługa Azure Machine Learning udostępnia token Azure Machine Learning do obliczenia przez użytkownika. Na przykład środowisko obliczeniowe usługi Machine Learning. Ten token jest używany przez usługę obliczeniową użytkownika do wywołania zwrotnego do usługi Azure Machine Learning (ogranicza zakres do obszaru roboczego) po zakończeniu przebiegu.

[![Zrzut ekranu przedstawiający sposób działania uwierzytelniania w usłudze Azure Machine Learning](./media/enterprise-readiness/authentication.png)](./media/enterprise-readiness/authentication-expanded.png)

### <a name="authentication-for-web-service-deployment"></a>Uwierzytelnianie dla wdrożenia usługi sieci Web

Azure Machine Learning obsługuje dwie formy uwierzytelniania dla usług sieci Web, klucza i tokenu. Każda usługa sieci Web może jednocześnie włączyć tylko jedną formę uwierzytelniania.

|Metoda uwierzytelniania|ACI|AKS|
|---|---|---|
|Klucz|Domyślnie wyłączone| Włączona domyślnie|
|Token| Niedostępny| Domyślnie wyłączone |

#### <a name="authentication-with-keys"></a>Uwierzytelnianie przy użyciu kluczy

Po włączeniu uwierzytelniania klucza dla wdrożenia automatycznie tworzone są klucze uwierzytelniania.

* Uwierzytelnianie jest domyślnie włączone podczas wdrażania w usłudze Azure Kubernetes Service.
* Podczas wdrażania programu w celu Azure Container Instances uwierzytelnianie jest domyślnie wyłączone.

Aby włączyć uwierzytelnianie klucza, użyj `auth_enabled` parametru podczas tworzenia lub aktualizowania wdrożenia.

Jeśli uwierzytelnianie klucza jest włączone, można użyć `get_keys` metody, aby pobrać podstawowy i pomocniczy klucz uwierzytelniania:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Jeśli musisz ponownie wygenerować klucz, użyj[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

#### <a name="authentication-with-tokens"></a>Uwierzytelnianie przy użyciu tokenów

Po włączeniu uwierzytelniania tokenów dla usługi sieci Web użytkownik musi podać do usługi sieci Web token Azure Machine Learning JSON, aby uzyskać do niego dostęp.

* Uwierzytelnianie tokenu jest domyślnie wyłączone podczas wdrażania w usłudze Azure Kubernetes Service.
* Uwierzytelnianie tokenu nie jest obsługiwane w przypadku wdrażania do Azure Container Instances.

Aby kontrolować uwierzytelnianie tokenu, użyj `token_auth_enabled` parametru podczas tworzenia lub aktualizowania wdrożenia.

Jeśli jest włączone uwierzytelnianie tokenu, można użyć `get_token` metody, aby pobrać token JWT i czas wygaśnięcia tokenu:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Po upływie `refresh_by` czasu tokenu trzeba będzie zażądać nowego tokenu.
>
> Zdecydowanie zalecamy utworzenie obszaru roboczego Azure Machine Learning w tym samym regionie, w którym znajduje się klaster usługi Azure Kubernetes. W celu uwierzytelnienia przy użyciu tokenu usługa sieci Web wykona wywołanie do regionu, w którym jest tworzony obszar roboczy Azure Machine Learning. Jeśli region obszaru roboczego jest niedostępny, nie będzie można pobrać tokenu dla usługi sieci Web nawet wtedy, gdy klaster znajduje się w innym regionie niż obszar roboczy. W efekcie uwierzytelnianie usługi Azure AD jest niedostępne, dopóki region obszaru roboczego nie będzie dostępny ponownie. Ponadto im większa odległość między regionem klastra a regionem obszaru roboczego, tym dłużej potrwa pobieranie tokenu.

## <a name="authorization"></a>Authorization

Możesz utworzyć wiele obszarów roboczych, a każdy obszar roboczy może być współużytkowany przez wiele osób. Po udostępnieniu obszaru roboczego można kontrolować do niego dostęp, przypisując do nich następujące role:

* Właściciel
* Współautor
* Czytelnik

W poniższej tabeli wymieniono niektóre główne operacje usługi Azure Machine Learning i role, które mogą je wykonać:

| Azure Machine Learning operacji usługi | Właściciel | Współautor | Czytelnik |
| ---- |:----:|:----:|:----:|
| Tworzenie obszaru roboczego | ✓ | ✓ | |
| Udostępnianie obszaru roboczego | ✓ | |  |
| Tworzenie obliczeń | ✓ | ✓ | |
| Dołącz obliczenia | ✓ | ✓ | |
| Dołącz magazyny danych | ✓ | ✓ | |
| Uruchamianie eksperymentu | ✓ | ✓ | |
| Wyświetl przebiegi/metryki | ✓ | ✓ | ✓ |
| Rejestrowanie modelu | ✓ | ✓ | |
| Tworzenie obrazu | ✓ | ✓ | |
| Wdróż usługę sieci Web | ✓ | ✓ | |
| Wyświetlanie modeli/obrazów | ✓ | ✓ | ✓ |
| Wywoływanie usługi sieci Web | ✓ | ✓ | ✓ |

Jeśli wbudowane role są niewystarczające dla Twoich potrzeb, można również utworzyć role niestandardowe. Jedyne obsługiwane role niestandardowe dotyczą operacji w obszarze roboczym i środowisko obliczeniowe usługi Machine Learning. Role niestandardowe mogą mieć uprawnienia do odczytu, zapisu lub usuwania w obszarze roboczym i w tym obszarze roboczym. Rolę można udostępnić na określonym poziomie obszaru roboczego, na poziomie określonego grupy zasobów lub na określonym poziomie subskrypcji. Aby uzyskać więcej informacji, zobacz [Zarządzanie użytkownikami i rolami w obszarze roboczym Azure Machine Learning](how-to-assign-roles.md)

### <a name="securing-compute-and-data"></a>Zabezpieczanie obliczeń i danych

Właściciele i Współautorzy mogą używać wszystkich obiektów docelowych obliczeń i magazynów danych, które są dołączone do obszaru roboczego.  
W każdym obszarze roboczym jest również skojarzona tożsamość zarządzana przypisana przez system (o takiej samej nazwie jak obszar roboczy) z następującymi uprawnieniami do dołączonych zasobów używanych w obszarze roboczym:

Aby uzyskać więcej informacji o tożsamościach zarządzanych, zobacz [zarządzane tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

| Resource | Uprawnienia |
| ----- | ----- |
| Obszar roboczy | Współautor |
| Konto magazynu | Współautor danych obiektu blob usługi Storage |
| Usługa Key Vault | Dostęp do wszystkich kluczy, wpisów tajnych, certyfikatów |
| Rejestr kontenerów platformy Azure | Współautor |
| Grupa zasobów, która zawiera obszar roboczy | Współautor |
| Grupa zasobów zawierająca Key Vault (jeśli różni się od niej zawierającej obszar roboczy) | Współautor |

Zaleca się, aby administratorzy nie odwołują dostępu do tożsamości zarządzanej do zasobów wymienionych powyżej. Dostęp można przywrócić przy użyciu operacji ponownych synchronizacji kluczy.

Usługa Azure Machine Learning tworzy dodatkową aplikację (nazwa rozpoczyna się od `aml-`) z dostępem poziomu współautor w ramach subskrypcji dla każdego regionu obszaru roboczego. Na przykład Jeśli masz obszar roboczy w regionie Wschodnie stany USA i inny obszar roboczy w Europie Północnej w tej samej subskrypcji, zobaczysz dwie takie aplikacje. Jest to zbędny sposób, aby usługa Azure Machine Learning mogła pomóc zarządzać zasobami obliczeniowymi.

## <a name="network-security"></a>Bezpieczeństwo sieci

Usługa Azure Machine Learning opiera się na innych usługach platformy Azure dla zasobów obliczeniowych. Zasoby obliczeniowe (cele obliczeniowe) są używane do uczenia i wdrażania modeli. Te obiekty docelowe obliczeń można tworzyć wewnątrz sieci wirtualnej. Na przykład można użyć Data Science Virtual Machine firmy Microsoft do uczenia modelu, a następnie wdrożenia modelu w usłudze Azure Kubernetes Service (AKS).  

Aby uzyskać więcej informacji, zobacz [jak uruchamiać eksperymenty i wnioskowania w sieci wirtualnej](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Szyfrowanie danych

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Usługa Azure Machine Learning przechowuje migawki, dane wyjściowe i dzienniki na koncie usługi Azure Blob Storage, które jest powiązane z obszarem roboczym usługi Azure Machine Learning i mieszka w subskrypcji użytkownika. Wszystkie dane przechowywane w usłudze Azure Blob Storage są szyfrowane w stanie spoczynku przy użyciu kluczy zarządzanych przez firmę Microsoft.

Aby uzyskać więcej informacji na temat sposobu przenoszenia własnych kluczy dla danych przechowywanych w usłudze Azure Blob Storage, zobacz [szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

Dane szkoleniowe są zazwyczaj przechowywane w usłudze Azure Blob Storage, dzięki czemu można uzyskać dostęp do zasobów obliczeniowych. Ten magazyn nie jest zarządzany przez program Azure Machine Learning ale został zainstalowany do obliczeń jako zdalny system plików.

Informacje o ponownym generowaniu kluczy dostępu dla kont usługi Azure Storage używanych z Twoim obszarem roboczym znajdują się w artykule ponowne [generowanie kluczy dostępu do magazynu](how-to-change-storage-access-key.md) .

#### <a name="cosmos-db"></a>Cosmos DB

Usługa Azure Machine Learning przechowuje metryki i metadane do Cosmos DB, które znajdują się w ramach subskrypcji firmy Microsoft zarządzanej przez usługę Azure Machine Learning. Wszystkie dane przechowywane w Cosmos DB są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft.

#### <a name="azure-container-registry-acr"></a>Azure Container Registry (ACR)

Wszystkie obrazy kontenerów w rejestrze (ACR) są szyfrowane w stanie spoczynku. Platforma Azure automatycznie szyfruje obraz przed jego zapisaniem i odszyfruje go na bieżąco, gdy usługa Azure Machine Learning pobierze obraz.

#### <a name="machine-learning-compute"></a>Środowisko obliczeniowe usługi Machine Learning

Dysk systemu operacyjnego dla każdego węzła obliczeniowego jest przechowywany w usłudze Azure Storage przy użyciu kluczy zarządzanych przez firmę Microsoft w ramach kont magazynu usługi Azure Machine Learning. Ten element docelowy obliczeń jest nieulotny, a klastry są zwykle skalowane, gdy nie ma żadnych uruchomionych w kolejce. Podstawowa maszyna wirtualna jest nieobsługiwana i dysk systemu operacyjnego został usunięty. Usługa Azure Disk Encryption nie jest obsługiwana w przypadku dysku systemu operacyjnego.
Każda maszyna wirtualna ma także lokalny dysk tymczasowy dla operacji systemu operacyjnego. Dysk może być również opcjonalnie używany do przygotowywania danych szkoleniowych. Dysk nie jest szyfrowany.
Aby uzyskać więcej informacji na temat sposobu, w jaki szyfrowanie w spoczynku działa na platformie Azure, zobacz [szyfrowanie danych na platformie Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest).

### <a name="encryption-in-transit"></a>Szyfrowanie podczas przesyłania

Komunikacja wewnętrzna między różnymi Azure Machine Learning mikrousług i zewnętrzna komunikacja wywołująca punkt końcowy oceniania jest obsługiwana przy użyciu protokołu SSL. Cały dostęp do usługi Azure Storage odbywa się również za pośrednictwem bezpiecznego kanału.
Aby uzyskać więcej informacji, zobacz [Secure Azure Machine Learning Web Services przy użyciu protokołu SSL](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Używanie Azure Key Vault

Key Vault wystąpienie skojarzone z obszarem roboczym jest używane przez usługę Azure Machine Learning do przechowywania poświadczeń różnych rodzajów:

* Powiązane parametry połączenia konta magazynu
* Hasła do wystąpień repozytorium kontenerów platformy Azure
* Parametry połączenia z magazynami danych.

Hasła SSH i klucze do elementów docelowych obliczeń, takich jak HDI HDInsight i VM, są przechowywane w osobnym Key Vault, który jest skojarzony z subskrypcją firmy Microsoft. Usługa Azure Machine Learning nie przechowuje żadnych haseł ani kluczy dostarczonych przez użytkownika zamiast nie generuje, autoryzuje i przechowuje własne klucze SSH w celu nawiązania połączenia z maszyną wirtualną/HDInsight w celu uruchomienia eksperymentów.
Każdy obszar roboczy ma skojarzoną tożsamość zarządzaną przez system (o takiej samej nazwie jak obszar roboczy), która ma dostęp do wszystkich kluczy, wpisów tajnych i certyfikatów w Key Vault.

## <a name="monitoring"></a>Monitorowanie

### <a name="metrics"></a>Metryki

Metryki Azure Monitor mogą służyć do wyświetlania i monitorowania metryk dla obszaru roboczego usługi Azure Machine Learning. Na [Azure Portal](https://portal.azure.com)wybierz swój obszar roboczy, a następnie użyj linku __metryki__ .

[![Zrzut ekranu przedstawiający przykładowe metryki dla obszaru roboczego](./media/enterprise-readiness/workspace-metrics.png)](./media/enterprise-readiness/workspace-metrics-expanded.png)

Metryki obejmują informacje o uruchomieniach, wdrożeniach i rejestracjach.

Aby uzyskać więcej informacji, zobacz [metryki w Azure monitor](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Dziennik aktywności

Dziennik aktywności można wyświetlić w obszarze roboczym, aby zobaczyć różne operacje wykonywane w obszarze roboczym i uzyskać podstawowe informacje, takie jak nazwa operacji, zdarzenie zainicjowane przez, sygnatura czasowa itd.

Poniższy zrzut ekranu przedstawia dziennik aktywności obszaru roboczego:

[![Zrzut ekranu przedstawiający dziennik aktywności w obszarze roboczym](./media/enterprise-readiness/workspace-activity-log.png)](./media/enterprise-readiness/workspace-activity-log-expanded.png)

Szczegóły żądania oceniania są przechowywane w usłudze Application Insights, która jest tworzona w ramach subskrypcji użytkownika podczas tworzenia obszaru roboczego. Zarejestrowane informacje zawierają pola, takie jak HTTPMethod, UserAgent, Computetype, RequestUrl, StatusCode, IdentyfikatorŻądania, Duration itd.

> [!IMPORTANT]
> Niektóre akcje w obszarze roboczym Azure Machine Learning nie rejestrują informacji w dzienniku aktywności. Na przykład uruchomienie szkolenia lub zarejestrowanie modelu.
>
> Niektóre z tych akcji są wyświetlane w obszarze __działania__ obszaru roboczego, ale nie wskazują, kto zainicjował działanie.

## <a name="data-flow-diagram"></a>Diagram przepływu danych

### <a name="create-workspace"></a>Tworzenie obszaru roboczego

Na poniższym diagramie przedstawiono przepływ pracy tworzenia obszaru roboczego.
Użytkownik loguje się do usługi Azure AD z dowolnego obsługiwanego klienta usługi Azure Machine Learning (interfejs wiersza polecenia, zestaw SDK języka Python, Azure Portal) i żąda odpowiedniego tokenu Azure Resource Manager. Następnie użytkownik wywołuje Azure Resource Manager, aby utworzyć obszar roboczy.  Azure Resource Manager skontaktować się z dostawcą zasobów usługi Azure Machine Learning w celu udostępnienia obszaru roboczego.  Dodatkowe zasoby są tworzone w ramach subskrypcji klienta podczas tworzenia obszaru roboczego:

* Magazyn kluczy (do przechowywania wpisów tajnych)
* Konto usługi Azure Storage (w tym dołączenie & obiektów BLOB)
* Azure Container Registry (do przechowywania obrazów platformy Docker na potrzeby wnioskowania/oceny i eksperymentowania)
* Application Insights (do przechowywania danych telemetrycznych)

Inne obliczenia dołączone do obszaru roboczego (usługa Azure Kubernetes, maszyna wirtualna itp.) mogą być również obsługiwane przez klientów zgodnie z potrzebami.

[![Zrzut ekranu przedstawiający przepływ pracy tworzenia obszaru roboczego](./media/enterprise-readiness/create-workspace.png)](./media/enterprise-readiness/create-workspace-expanded.png)

### <a name="save-source-code-training-scripts"></a>Zapisz kod źródłowy (skrypty szkoleniowe)

Na poniższym diagramie przedstawiono przepływ pracy migawek kodu.
Skojarzona z obszarem roboczym usługi Azure Machine Learning są katalogami (eksperymenty), które zawierają kod źródłowy (skrypty szkoleniowe).  Te skrypty są przechowywane na lokalnym komputerze klienta i w chmurze (w Blob Storage na platformie Azure w ramach subskrypcji klienta). Migawki kodu są używane do wykonywania lub inspekcji inspekcji historycznej.

[![Zrzut ekranu przedstawiający przepływ pracy tworzenia obszaru roboczego](./media/enterprise-readiness/code-snapshot.png)](./media/enterprise-readiness/code-snapshot-expanded.png)

### <a name="training"></a>Szkolenia

Na poniższym diagramie przedstawiono przepływ pracy szkoleniowej.

* Usługa Azure Machine Learning jest wywoływana z IDENTYFIKATORem migawki dla migawki kodu zapisanej powyżej
* Usługa Azure Machine Learning tworzy identyfikator uruchomienia (opcjonalnie) & token usługi Azure Machine Learning, który jest później używany przez cele obliczeniowe, takie jak środowisko obliczeniowe usługi Machine Learning/VM, aby porozmawiać z usługą Azure Machine Learning
* Można wybrać zarządzane obliczenia (np. Środowisko obliczeniowe usługi Machine Learning) lub obliczeń niezarządzanych (np. Maszyna wirtualna) do uruchamiania zadań szkoleniowych. Przepływ danych został wyjaśniony dla poniższych scenariuszy:
* (Maszyna wirtualna/HDInsight — dostęp z użyciem poświadczeń SSH w Key Vault w ramach subskrypcji firmy Microsoft) Usługa Azure Machine Learning uruchamia kod zarządzania w miejscu docelowym obliczeń, który:

   1. Przygotowuje środowisko. (Platforma Docker jest opcją dla maszyny wirtualnej i lokalnego. Aby dowiedzieć się, jak działa środowisko Docker, należy zapoznać się z poniższymi krokami środowisko obliczeniowe usługi Machine Learning.
   1. Pobiera kod.
   1. Konfiguruje zmienne środowiskowe i konfiguracje.
   1. Uruchamia skrypt użytkownika (migawka kodu wymieniona powyżej).

* (Środowisko obliczeniowe usługi Machine Learning — dostęp przy użyciu tożsamości zarządzanej przez obszar roboczy) Ponieważ środowisko obliczeniowe usługi Machine Learning jest zarządzanym obliczaniem, który jest zarządzany przez firmę Microsoft, w związku z czym jest uruchamiany w ramach subskrypcji firmy Microsoft.

   1. Zdalna konstrukcja platformy Docker jest wyłączona, w razie konieczności.
   1. Zapisuje kod zarządzania w udziale użytkownika platformy Azure.
   1. Uruchamia kontener za pomocą polecenia początkowego, czyli kodu zarządzania zgodnie z opisem w poprzednim kroku.

#### <a name="querying-runs-and-metrics"></a>Wykonywanie zapytań dotyczących przebiegów i metryk

Ten krok jest pokazywany w przepływie, w którym obliczenia szkoleniowe zapisują *metryki uruchamiania* z powrotem do usługi Azure Machine Learning, z której są przechowywane w Cosmos DB. Klienci mogą wywoływać usługę Azure Machine Learning, która będzie w stanie wyłączać metryki ściągania z Cosmos DB i zwracać ją z powrotem do klienta.

[![Zrzut ekranu przedstawiający przepływ pracy tworzenia obszaru roboczego](./media/enterprise-readiness/training-and-metrics.png)](./media/enterprise-readiness/training-and-metrics-expanded.png)

### <a name="creating-web-services"></a>Tworzenie usług sieci Web

Na poniższym diagramie przedstawiono przepływ pracy wnioskowania. Wnioskowanie lub ocenianie modelu to faza, w której wdrożony model jest używany do prognozowania, najczęściej dotyczący danych produkcyjnych.
Zobacz szczegóły poniżej:

* Użytkownik rejestruje model przy użyciu klienta, takiego jak Azure ML SDK
* Użytkownik tworzy obraz przy użyciu modelu, pliku wynikowego i innych zależności modelu
* Obraz platformy Docker jest tworzony i przechowywany w ACR
* Usługa WebService jest wdrażana w obiekcie docelowym obliczeń (ACI/AKS) przy użyciu utworzonego powyżej obrazu
* Szczegóły żądania oceniania są przechowywane w usłudze Application Insights, która jest w subskrypcji użytkownika
* Dane telemetryczne są również wypychane do subskrypcji firmy Microsoft/platformy Azure

[![Zrzut ekranu przedstawiający przepływ pracy tworzenia obszaru roboczego](./media/enterprise-readiness/inferencing.png)](./media/enterprise-readiness/inferencing-expanded.png)

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczania usług sieci web Azure Machine Learning przy użyciu protokołu SSL](how-to-secure-web-service.md)
* [Korzystanie z modelu uczenia Maszynowego, wdrożyć jako usługę sieci web](how-to-consume-web-service.md)
* [Jak uruchomić prognoz usługi batch](how-to-run-batch-predictions.md)
* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
* [Zestaw SDK usługi Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Korzystanie z usługi Azure Machine Learning z sieciami wirtualnymi platformy Azure](how-to-enable-virtual-network.md)
* [Najlepsze rozwiązania dotyczące kompilowania systemów rekomendacji](https://github.com/Microsoft/Recommenders)
* [Kompilowanie interfejsu API rekomendacji w czasie rzeczywistym na platformie Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
