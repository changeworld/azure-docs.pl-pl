---
title: Omówienie pojęć dotyczących Zarządzanie modelami w usłudze Azure Machine Learning | Dokumentacja firmy Microsoft
description: Ten dokument zawiera wyjaśnienie założeń modelu zarządzania dla usługi Azure Machine Learning.
services: machine-learning
author: hjerezmsft
ms.author: hjerez
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 041f7147f171514d941555ff2f6144bac2062b06
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "42055973"
---
# <a name="azure-machine-learning-model-management"></a>Zarządzanie modelami w usłudze Azure Machine Learning

Platformy Azure Machine Learning zarządzania modelami umożliwia wdrażanie przepływów pracy i modeli usługi machine learning i zarządzanie nimi. 

Zarządzanie modelami w usłudze zapewnia funkcje:
- Przechowywanie wersji modelu
- Śledzenia modeli w środowisku produkcyjnym
- Wdrażanie modeli do środowiska produkcyjnego, za pośrednictwem środowiska obliczeniowego usługi Azure ml z [usługi Azure Container Service](https://azure.microsoft.com/services/container-service/) i [Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- Tworzenie kontenerów platformy Docker za pomocą modeli i ich testowania lokalnie
- Ponowne szkolenie modelu automatycznych
- Przechwytywanie modelu telemetrii, aby uzyskać szczegółowe informacje. 

Zarządzanie modelami usługi Azure Machine Learning udostępnia rejestr wersji modelu. Zapewnia także zautomatyzowane przepływy pracy pakowanie i wdrażanie kontenerów uczenia maszynowego jako interfejsy API REST. Modele i ich zależności środowiska uruchomieniowego są spakowane w kontenerze Docker opartych na systemie Linux przy użyciu interfejsu API prognoz. 

Azure Machine Learning środowisk obliczeniowych ułatwiają konfigurowanie klastrów i zarządzanie nimi Skalowalna do hostowania modeli. Środowisko obliczeniowe jest oparta na usłudze Azure Container Service. Usługa Azure Container Service zapewnia automatyczne narażenia interfejsy API usługi Machine Learning jako punkty końcowe interfejsu API REST, dzięki następującym funkcjom:

- Authentication
- Równoważenie obciążenia
- Automatyczne skalowanie w poziomie
- Szyfrowanie

Zarządzanie modelami usługi Azure Machine Learning spełnia te wymagania przy użyciu interfejsu wiersza polecenia, interfejs API i portalu Azure. 

Zarządzanie modelami w usłudze Azure Machine Learning korzysta z następujących informacji:

 - Model pliku lub katalogu przy użyciu plików modelu
 - Użytkownik utworzył plik w języku Python Implementowanie modelem oceniania — funkcja
 - Pliku zależności Conda, zawierającego listę zależności środowiska uruchomieniowego
 - Wybór środowiska czasu wykonywania, a 
 - Plik schematu dla parametrów interfejsu API 

Te informacje są używane podczas wykonywania następujących czynności:

- Rejestrowanie modelu
- Tworzenie manifestu, który jest używany podczas tworzenia kontenera
- Tworzenie obrazu kontenera platformy Docker
- Wdrażanie kontenera w usłudze Azure Container Service
 
Na poniższej ilustracji przedstawiono omówienie sposobu zarejestrowany i wdrożony do klastra modeli. 

![](media/model-management-overview/modelmanagement.png)

## <a name="create-and-manage-models"></a>Tworzenie modeli i zarządzania nimi 
Za zarejestrowanie modeli i zarządzanie modelami w usłudze Azure Machine Learning do śledzenia wersji modelu w środowisku produkcyjnym. W celu ułatwienia odtwarzaniem i nadzór usługi przechwytuje wszystkie zależności i skojarzone informacje. Aby uzyskać lepszy wgląd w wydajność można przechwycić dane telemetryczne modelu za pomocą dostarczonego zestawu SDK. Model danych telemetrycznych jest archiwizowane w magazynie użytkownika. Model danych telemetrycznych można później do analizowania wydajności modelu, ponownego trenowania i uzyskiwanie szczegółowych informacji w firmie.

## <a name="create-and-manage-manifests"></a>Tworzenie i zarządzanie nimi manifestów 
Modele wymagają dodatkowych artefakty do wdrożenia w środowisku produkcyjnym. System zapewnia możliwość tworzenia manifestu, który obejmuje model, zależności, skrypt wnioskowania (zwane również oceniania skrypt), przykładowe dane, schemat itp. Tego manifestu działa jako przepisu, aby utworzyć obraz kontenera Docker. Przedsiębiorstwa można automatycznie wygenerować manifest, utworzyć różne wersje i zarządzać ich manifestów. 

## <a name="create-and-manage-docker-container-images"></a>Utwórz i Zarządzaj obrazami kontenerów platformy Docker 
Kompilowanie obrazów kontenerów aparatu Docker w swoich środowiskach odpowiednich, można użyć manifestu z poprzedniego kroku. Obrazy konteneryzowanych, opartych na platformie Docker udostępniają przedsiębiorstwom elastyczność, aby uruchomić te obrazy w następujących środowiskach obliczeniowych:

- [Kubernetes na podstawie usługi Azure Container Service](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- Usługi kontenerów w środowisku lokalnym
- Środowiska deweloperskie
- Urządzenia IoT

Obrazy te platformy Docker w konteneryzowanych są niezależne z wszystkie niezbędne zależności wymagane do generowania przewidywań. 

## <a name="deploy-docker-container-images"></a>Wdrażanie obrazów kontenerów platformy Docker 
Usługa w Zarządzanie modelami usługi Azure Machine Learning umożliwia wdrażanie obrazów kontenera aparatu Docker za pomocą jednego polecenia do usługi Azure Container Service zarządza środowiska obliczeniowego usługi ML. Te wdrożenia są tworzone przy użyciu serwera frontonu, który udostępnia następujące funkcje:

- Prognozy małe opóźnienia na dużą skalę
- Równoważenie obciążenia
- Automatyczne skalowanie punktów końcowych ML
- Autoryzacji klucza interfejsu API
- Dokument struktury swagger interfejsu API

Można kontrolować skali wdrożenia i dane telemetryczne za pomocą następujących ustawień konfiguracji:

- Rejestrowanie systemu i telemetrii modelu dla poszczególnych poziomów usług sieci web. Jeśli włączone, wszystkie dzienniki stdout są przesyłane strumieniowo do [usługi Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Model danych telemetrycznych jest archiwizowane w magazynie, który należy podać. 
- Limity skalowania automatycznego i współbieżności. Te ustawienia są automatycznie zwiększać liczbę wdrożonych kontenerów, w oparciu o obciążenie w ramach istniejącego klastra. Kontrolują one także przepływność i spójność prognozowania opóźnień.

## <a name="consumption"></a>Zużycie 
Zarządzanie modelami usługi Azure Machine Learning tworzy wdrożony model wraz z dokumentu swagger interfejsu API REST. Za pośrednictwem wywołania interfejsów API REST przy użyciu interfejsu API klucza i modelować dane wejściowe, aby uzyskiwać prognozy jako część aplikacji line-of-business, mogą wykorzystywać wdrożonych modelach. Przykładowy kod w językach Java, znajduje się w usłudze GitHub [Python](https://github.com/CortanaAnalyticsGallery-Int/digit-recognition-cnn-tf/blob/master/client.py)i języka C# do wywoływania interfejsów API REST. Machine Learning Model zarządzania wiersza polecenia platformy Azure zapewnia łatwy sposób pracy z tych interfejsów API REST. Będzie można korzystać za pośrednictwem interfejsów API za pomocą jednego polecenia interfejsu wiersza polecenia aplikacji obsługujący strukturę swagger lub przy użyciu programu curl. 

## <a name="retraining"></a>Ponowne szkolenie 
Zarządzanie modelami usługi Azure Machine Learning udostępnia interfejsy API, która umożliwia ponowne trenowanie modeli. Można również użyć interfejsów API aktualizacji istniejących wdrożeń za pomocą zaktualizowanych wersji modelu. Jako część przepływu pracy analizy danych ponowne utworzenie modelu w Twoim środowisku eksperymentowania. Następnie zarejestrować model, w przypadku zarządzania modelami i zaktualizować istniejące wdrożenia. Aktualizacje są wykonywane za pomocą jednego polecenia interfejsu wiersza polecenia UPDATE. Polecenie aktualizacji aktualizuje istniejące wdrożenia bez zmiany adresu URL interfejsu API lub klucz. Aplikacje używające modelu nadal działać bez zmian kodu i rozpoczęcie uzyskiwania trafniejsze przy użyciu nowego modelu.

Pełny przepływ pracy opisujące te pojęcia są przechwytywane na poniższej ilustracji:

![](media/model-management-overview/modelmanagementworkflow.png)

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania 
- **Jakie typy danych są obsługiwane? Czy można przekazać tablic NumPy, bezpośrednio jako dane wejściowe do usługi sieci web?**

   Jeśli udostępniasz pliku schematu, który został utworzony przy użyciu zestawu SDK generate_schema, następnie można przekazać NumPy i/lub Pandas DF. Można również przekazać wszystkie dane wejściowe do serializacji JSON. Obraz można przekazać jako zakodowany ciąg binarny także.

- **Usługę sieci web obsługuje wielu danych wejściowych ani przeanalizować inne dane wejściowe**

   Tak, możesz korzystać z wielu danych wejściowych w pakiecie w jednym żądaniu JSON w formie słownika. Każdy danych wejściowych, które będzie odpowiadać pojedynczego słownika Unikatowy klucz.

- **Jest wywołania blokowania lub wywołanie asynchroniczne wywołanie aktywowany przez żądanie sieci web usługi?**

   Jeśli usługa została utworzona za pomocą opcji w czasie rzeczywistym w ramach interfejsu wiersza polecenia lub interfejsu API, to blokuje synchroniczne wywołanie. Okazuje się szybko w czasie rzeczywistym. Mimo, że po stronie klienta, można też wywołać przy użyciu biblioteki async HTTP, aby uniknąć blokowania wątku klienta.

- **Ile żądań może jednocześnie obsługiwać usługę sieci web?**

   To zależy od skali usługi sieci web i klastra. Można skalować w poziomie usługi, do 100 razy replik, a następnie może obsługiwać wiele żądań jednocześnie. Można również skonfigurować maksymalny równoczesnych żądań na replikę w celu zwiększenia przepływności usługi.

- **Ile żądań usługi sieci web ustawić kolejkę?**

   Można to zrobić. Domyślnie jest równa 10 ~ na pojedynczą replikę, ale możesz zwiększyć/zmniejszyć go do wymagań aplikacji. Zazwyczaj jej zwiększanie liczba zakolejkowanych żądań zwiększa przepustowość usługi, ale sprawia, że zadecydować opóźnienia w percentylach wyższy. Aby zachować opóźnienia spójne, może chcesz ustawić kolejkowania niskiej wartości (1-5) i zwiększenia liczby replik w celu obsługi informacji o przepływności. Można również włączyć Skalowanie automatyczne, aby liczba replik, dostosowując automatycznie na podstawie przy ładowaniu. 

- **Tej samej maszyny lub klastra umożliwia wiele punktów końcowych usługi sieci web?**

   Naturalnie. 100 razy usług/punkty końcowe można uruchamiać na tym samym klastrze. 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać wprowadzenie do zarządzania modelami, zobacz [Konfigurowanie zarządzania modelami](deployment-setup-configuration.md).
