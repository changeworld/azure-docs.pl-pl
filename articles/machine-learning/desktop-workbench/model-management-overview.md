---
title: "Omówienie pojęć dotyczących Azure Machine Learning Model zarządzania | Dokumentacja firmy Microsoft"
description: "W tym dokumencie opisano pojęcia Model zarządzania dla usługi Azure Machine Learning."
services: machine-learning
author: nk773
ms.author: padou
manager: mwinkle
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 89f95753248f74c7f6cb9ca1f680a01b07dd43d1
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
---
# <a name="azure-machine-learning-model-management"></a>Zarządzanie modelami w usłudze Azure Machine Learning

Azure Machine Learning Management modelu pozwala na zarządzanie i wdrażanie przepływów pracy i modeli uczenia maszynowego. 

Model zarządzania zapewnia funkcje dla:
- Przechowywanie wersji modelu
- Modele śledzenia w środowisku produkcyjnym
- Wdrażanie modeli w środowisku produkcyjnym za pośrednictwem środowiska obliczeniowe uczenie maszynowe Azure z [usługi kontenera platformy Azure](https://azure.microsoft.com/services/container-service/) i [Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- Tworzenie kontenerów Docker z modelami i testowanie je lokalnie
- Model automatycznego ponownego trenowania
- Przechwytywania danych telemetrycznych modelu dla przydatnych wyników analiz. 

Azure Machine Learning modelu zarządzania udostępnia rejestr wersji modelu. Zapewnia także automatycznych przepływów pracy pakowanie i wdrażanie kontenerów uczenia maszynowego jako interfejsów API REST. Modele oraz ich zależności środowiska uruchomieniowego są umieszczone w kontenerze Docker opartych na systemie Linux z prognozowania interfejsu API. 

Azure Machine Learning obliczeniowe środowiska Pomoc do konfigurowania i zarządzania nią klastrów skalowalnych hostingu modeli. Środowiska obliczeniowe zależy od usługi kontenera platformy Azure. Usługi kontenera platformy Azure zapewnia automatyczne narażenia Machine Learning API jako punkty końcowe interfejsu API REST przy użyciu następujących funkcji:

- Authentication
- Równoważenie obciążenia
- Automatycznego skalowania w poziomie
- Szyfrowanie

Azure Machine Learning modelu zarządzania zapewnia te możliwości za pomocą interfejsu wiersza polecenia, interfejs API i portalu Azure. 

Azure Machine Learning model zarządzania używa następujących informacji:

 - Model pliku lub katalogu z plików modeli
 - Użytkownik utworzył plik Python implementacja modelu oceniania — funkcja
 - Conda zależności pliku zawierającego listę zależności środowiska uruchomieniowego
 - Środowisko uruchomieniowe środowiska wyboru, a 
 - Plik schematu dla parametrów interfejsu API 

Te informacje są używane podczas wykonywania następujących czynności:

- Rejestrowanie modelu
- Tworzenie manifestu, który jest używany podczas tworzenia kontenera
- Tworzenie obrazu w kontenerze Docker
- Wdrażanie kontenerem usługi kontenera platformy Azure
 
Na poniższej ilustracji przedstawiono omówienie sposobu modeli są rejestrowane i wdrożonych w klastrze. 

![](media/model-management-overview/modelmanagement.png)

## <a name="create-and-manage-models"></a>Tworzenie i zarządzanie nimi modeli 
Możesz zarejestrować modeli z usługą Azure Machine Learning Model Management śledzenia wersji modelu w środowisku produkcyjnym. W celu ułatwienia powtarzalności i nadzór nad usługi przechwytuje wszystkie zależności i skojarzonych informacji. Uzyskać lepszy wgląd w wydajności można przechwycić telemetrii modelu przy użyciu podanego zestawu SDK. Dane telemetryczne modelu zostaną zarchiwizowane w magazynie określonego przez użytkownika. Dane telemetryczne modelu można później do analizowania wydajności modelu, ponownego trenowania i uzyskaniu szczegółowych informacji w firmie.

## <a name="create-and-manage-manifests"></a>Tworzenie i zarządzanie nimi manifestów 
Modele wymagają dodatkowych artefakty do wdrożenia w środowisku produkcyjnym. System umożliwia tworzenie manifestu, który obejmuje modelu, zależności, wnioskowania skryptu (alias oceniania skryptu), dane przykładowe, schematu itp. Tego manifestu działa jako przepisu, aby utworzyć obraz kontenera Docker. Przedsiębiorstwa mogą automatycznego generowania manifestu, utwórz różne wersje i Zarządzaj ich manifestów. 

## <a name="create-and-manage-docker-container-images"></a>Tworzenie i zarządzanie obrazami kontenera Docker 
Manifest z poprzedniego kroku służy do tworzenia obrazów na podstawie Docker kontenera w swoich środowiskach odpowiednich. Obrazy konteneryzowanych, na podstawie Docker dostarczyć przedsiębiorstwom możliwość uruchomienia tych obrazów w następujących środowiskach obliczeniowych:

- [Kubernetes na podstawie usługi kontenera platformy Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- Lokalne usługi kontenera
- Środowisk deweloperskich
- Urządzenia IoT

Obrazy te Docker w konteneryzowanych są niezależne wszystkie zależności niezbędne wymagany do generowania prognoz. 

## <a name="deploy-docker-container-images"></a>Wdrażanie obrazów kontenera Docker 
Z usługi Azure Machine Learning modelu Management można wdrożyć usługi kontenera platformy Azure zarządza środowiska obliczeniowe ML kontenera Docker na podstawie obrazów za pomocą jednego polecenia. Te wdrożenia są tworzone na serwerze frontonu, który udostępnia następujące funkcje:

- Małe opóźnienia prognoz wielkoskalowych
- Równoważenie obciążenia
- Automatyczne skalowanie punktów końcowych ML
- Autoryzacji klucza interfejsu API
- Interfejs API programu swagger dokumentu

Można kontrolować skali wdrożenia i dane telemetryczne za pośrednictwem następujących ustawień konfiguracji:

- Rejestrowanie systemu i dane telemetryczne modelu dla każdego poziomu usługi sieci web. Jeśli opcja jest włączona, wszystkie dzienniki stdout są przesyłane strumieniowo do [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Dane telemetryczne modelu zostaną zarchiwizowane w magazynie, które należy podać. 
- Automatyczne skalowanie i współbieżność limity. Te ustawienia automatycznie zwiększyć liczbę wdrożone kontenery oparte na obciążenia w ramach istniejącego klastra. Również decydować, przepływności i spójności prognozowania opóźnienia.

## <a name="consumption"></a>Zużycie 
Azure Machine Learning modelu zarządzania tworzy interfejsu API REST dla wdrożonych modelu wraz z dokumentu programu swagger. Korzystać z modeli wdrożone, przez wywoływanie interfejsów API REST z interfejsem API klucza i model danych wejściowych w celu pobrania prognozy jako część aplikacji biznesowych z. Przykładowy kod jest dostępny w witrynie GitHub dla języków Java, [Python](https://github.com/CortanaAnalyticsGallery-Int/digit-recognition-cnn-tf/blob/master/client.py)i C# do wywoływania interfejsów API REST. Wiersza polecenia platformy Azure Machine Learning Model zarządzania zapewnia prosty sposób pracy z tych interfejsów API REST. Można używać interfejsów API za pomocą jednego polecenia interfejsu wiersza polecenia, aplikacje korzystające z programu swagger, lub przy użyciu programu curl. 

## <a name="retraining"></a>Ponownego trenowania 
Azure Machine Learning modelu zarządzania zawiera interfejsy API, które umożliwiają ponownie ucz modele programu. Interfejsy API umożliwia również zaktualizować istniejące wdrożenia o zaktualizowanych wersji modelu. Jako część pracy do analizy danych utwórz ponownie modelu w danym środowisku eksperymenty. Następnie zarejestruj modelu z zarządzania w modelu, a istniejące wdrożenia aktualizacji. Aktualizacji są wykonywane za pomocą jednego polecenia interfejsu wiersza polecenia aktualizacji. Polecenie aktualizacji aktualizuje istniejące wdrożenia bez zmiany adresu URL interfejsu API i klucz. Aplikacje używające modelu nadal będzie działać bez zmiany kodu, a następnie zacznij uzyskiwać lepsze prognoz przy użyciu nowego modelu.

Pełny przepływ pracy opisujące tych pojęć jest przechwytywany na poniższej ilustracji:

![](media/model-management-overview/modelmanagementworkflow.png)

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania 
- **Jakie typy danych są obsługiwane? Czy można przekazać tablice NumPy, bezpośrednio jako dane wejściowe do usługi sieci web?**

   Jeśli udostępniasz pliku schematu, który został utworzony przy użyciu zestawu SDK generate_schema następnie można przekazać NumPy i/lub Pandas DF. Można również przekazać nakładów serializacji JSON. Obraz można przekazać jako zakodowany ciąg binarny również.

- **Usługa sieci web obsługuje wielu danych wejściowych lub przeanalizować różnych komponentów?**

   Tak, może potrwać wielu danych wejściowych, umieszczone w jednym żądaniu JSON w formie słownika. Każdy danych wejściowych odpowiada jednego słownika Unikatowy klucz.

- **Jest blokowania wywołania lub wywołanie asynchroniczne wywołanie aktywowany przez żądania sieci web usługi?**

   Jeśli usługa została utworzona przy użyciu opcji czasu rzeczywistego ramach interfejsu wiersza polecenia lub interfejsu API, to blokuje synchroniczne wywołania. Oczekuje się być szybkiego w czasie rzeczywistym. Chociaż po stronie klienta, można wywołać za pomocą biblioteki async HTTP, aby uniknąć zablokowania wątku klienta.

- **Ile żądań może jednocześnie obsługiwać usługę sieci web?**

   To zależy od skali usługi sieci web i klastra. Możesz skalować w poziomie usługi do 100 x repliki, a następnie może obsługiwać wiele żądań jednocześnie. Można również skonfigurować maksymalny równoczesnych żądań dla repliki w celu zwiększenia przepływności usługi.

- **Ile żądań można kolejki usługi sieci web?**

   Można to zrobić. Domyślnie jest równa 10 ~ na pojedynczą replikę, ale użytkownik może zwiększenie/zmniejszenie go do wymagań aplikacji. Zazwyczaj zwiększenie jego liczba żądań w kolejce zwiększa przepustowość usługi, ale powoduje, że co gorsza opóźnienia na wyższe percentylu. Do zachowania spójności opóźnienia, może chcesz ustawić kolejkowania niską wartość (1-5) i zwiększyć liczbę replik do obsługi przepływności. Można również włączyć funkcję skalowania automatycznego, aby liczba replik dostosowywania automatycznie na podstawie na obciążenia. 

- **Czy tego samego komputera lub klastra można używać do wielu punktów końcowych usługi sieci web?**

   Naturalnie. 100 x usług/punktów końcowych można uruchamiać na tym samym klastrze. 

## <a name="next-steps"></a>Kolejne kroki
Wprowadzenie do zarządzania w modelu, dla [Konfigurowanie zarządzania modelu](deployment-setup-configuration.md).
