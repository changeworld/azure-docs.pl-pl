---
title: Automatyzacja systemu operacyjnego i framework poprawki z Azure kontenera rejestru kompilacji (kompilacja ACR)
description: Wprowadzenie do ACR kompilacji zestaw funkcji w rejestrze kontenera platformy Azure, która zapewnia bezpieczny, automatyczne tworzenie obrazu kontenera i stosowanie poprawek w chmurze.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 04/30/2018
ms.author: marsma
ms.openlocfilehash: 0e10b415f3d1c7ca502cfd63ebd27a6adcb69635
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
---
# <a name="automate-os-and-framework-patching-with-acr-build"></a>Automatyzacja systemu operacyjnego i framework poprawki z ACR kompilacji

Kontenery Podaj nowe poziomy wirtualizacji Izolowanie aplikacji i deweloperów zależności od infrastruktury i wymagań operacyjnych. Jednak pozostałą, jest potrzeba adresów, jak jest poprawiono wirtualizacji tej aplikacji.

**Tworzenie ACR**, zestaw funkcji w rejestrze kontenera platformy Azure, zapewnia nie tylko natywny kontenera możliwości kompilacji obrazu, ale również automatyzuje [systemu operacyjnego i stosowanie poprawek framework](#automate-os-and-framework-patching) dla kontenerów Docker.

> [!IMPORTANT]
> ACR kompilacji jest obecnie w wersji zapoznawczej i jest obsługiwana tylko przez rejestrów kontenera platformy Azure w **wschodnie stany USA** i **Europa** regionów. Podglądy stają się dostępne dla Ciebie pod warunkiem, że zgadzasz się [uzupełniające warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="what-is-acr-build"></a>Co to jest ACR kompilacji?

Kontener Azure rejestru kompilacji jest usługi kompilacji obrazu macierzystego Azure kontenera. Kompilacje kompilacji ACR umożliwia pętli wewnętrzny Programowanie w chmurze za pomocą obrazu kontenera na żądanie, i zaktualizuj kompilacjach zautomatyzowanych na obraz, jak i zatwierdzania kodu źródłowego.

Wyzwalacz kontener obrazu automatycznie tworzy podczas kodu został przekazany do repozytorium Git lub po zaktualizowaniu obrazu podstawowego kontenera. Z wyzwalaczy aktualizacji obrazu podstawowego, można zautomatyzować, system operacyjny i platforma aplikacji poprawki przepływu pracy, zachowanie bezpiecznego środowiskach przestrzegając podmiotów niezmienne kontenerów.

## <a name="quick-build-inner-loop-extended-to-the-cloud"></a>Szybka kompilacja: wewnętrzny pętli rozszerzony do chmury

Początek — Zarządzanie cyklem życia uruchamiany przed deweloperzy zatwierdzić ich pierwszych wierszy kodu. Tworzenie ACR [szybkie tworzenie](container-registry-tutorial-quick-build.md) funkcji umożliwia obsługę zintegrowanego rozwoju lokalnych wewnętrzny pętli, Odciążanie kompilacji na platformie Azure. Z kompilacje szybkie możesz sprawdzić, do definicji kompilacji automatycznych przed wykonaniem kodu.

Przy użyciu znanych `docker build` format [kompilacji acr az] [ az-acr-build] polecenia interfejsu wiersza polecenia Azure pobierającej kontekst lokalnego, wysyła go do usługi kompilacji ACR i domyślnie wypychanie wbudowanych obrazu do jego rejestru na ukończenia. Kompilacji ACR następuje rejestrów z replikacją geograficzną włączenie zespoły deweloperów rozproszone wykorzystać najbliższego replikowanych rejestru. Podczas udostępniania wersji zapoznawczej kompilacja ACR jest dostępna w regionach wschodnie stany USA i Europa Zachodnia.

Tworzenie ACR został zaprojektowany jako pierwotnych cykl kontenera. Na przykład zintegrować ACR kompilacji rozwiązania CI/CD. Wykonując [logowania az] [ az-login] z [nazwy głównej usługi][az-login-service-principal], rozwiązania CI/CD może następnie wystawić [az acr kompilacji] [ az-acr-build] poleceń, aby rozpocząć obrazu kompilacji.

Dowiedz się, jak użyć szybkiego kompilacje w pierwszym samouczku kompilacji ACR [tworzenia obrazów kontenera w chmurze za pomocą kompilacji rejestru kontenera Azure](container-registry-tutorial-quick-build.md).

## <a name="automatic-build-on-source-code-commit"></a>Automatyczne kompilacji w zatwierdzeniu kodu źródłowego

Użyj ACR kompilacji automatycznie wyzwalać obrazu kontenera kompilacji, jeśli kod jest zaangażowana w repozytorium Git. Kompilowanie zadań, można skonfigurować przy użyciu polecenia interfejsu wiersza polecenia Azure [zadania kompilacji az acr][az-acr-build-task], pozwala na określenie repozytorium Git i opcjonalnie gałęzi i plik Dockerfile. Gdy zespół zatwierdza kod do repozytorium, element webhook kompilacji ACR utworzonych wyzwala kompilacji obrazu kontenera zdefiniowane w repozytorium.

Dowiedz się, jak można wyzwolić kompilacji w zatwierdzeniu kodu źródłowego w drugim samouczku ACR kompilacji [kompilacje Automatyzacja kontener obrazu z kompilacji rejestru kontenera Azure](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatyzacja systemu operacyjnego i stosowanie poprawek framework

Możliwości kompilacji ACR naprawdę ulepszyć planowaną kompilacji kontenera pochodzi z jego zdolność do wykrywania aktualizacji do obrazu podstawowego. Gdy zaktualizowano podstawowy obraz zostanie przypisany do rejestru, kompilacji ACR automatycznie można tworzyć żadnych obrazów aplikacji na ich podstawie.

Kontener obrazów można szeroko podzielić na *podstawowej* obrazów i *aplikacji* obrazów. Podstawowy obrazów zwykle zawierają systemu operacyjnego i platformy aplikacji, na których jest oparta aplikacji, oraz inne dostosowania. Te obrazy podstawowe są zazwyczaj na podstawie publicznego nadrzędnego obrazów, na przykład [Alpine Linux] [ base-alpine] lub [Node.js][base-node]. Kilka obrazów aplikacji mogą współdzielić wspólnej obrazu podstawowego.

Po zaktualizowaniu framework obrazu systemu operacyjnego lub aplikacji przez element utrzymujący nadrzędnego na przykład za pomocą krytyczne poprawki zabezpieczeń systemu operacyjnego, należy należy również zaktualizować podstawowej obrazów do poprawkę krytyczne. Każdego obrazu aplikacji musi następnie również zostać również przebudowany w celu zawierają te poprawki nadrzędnego teraz zawarta w obrazu podstawowego.

Ponieważ dynamicznie kompilacji ACR odnajdzie zależności obrazu podstawowego, podczas tworzenia obrazu kontenera, może wykryć po zaktualizowaniu obraz podstawowy obraz aplikacji. Jeden wstępnie [zadania kompilacji](container-registry-tutorial-base-image-update.md#create-build-task), ACR kompilacji następnie **automatycznie odtwarza każdego obrazu aplikacji** dla Ciebie. Z tego automatycznego wykrywania i ponownie skompilować, zapisuje ACR kompilacji obrazu możesz ilość czasu, zwykle wymagane do ręcznego śledzenia i zaktualizować każdej aplikacji odwołuje się do obrazu podstawowego zaktualizowane.

Więcej informacji na temat systemu operacyjnego i framework poprawki w trzeci samouczek tworzenia ACR [obrazu Automatyzacja opiera się na podstawowy obraz aktualizacji z kompilacji rejestru kontenera Azure](container-registry-tutorial-base-image-update.md).

> [!NOTE]
> Podczas tworzenia ACR jest w wersji zapoznawczej, wyzwalacz aktualizacji obrazu podstawowego tworzy tylko wtedy, gdy obrazy podstawową i aplikacji znajdują się w rejestrze tego samego kontenera platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

Gdy wszystko jest gotowe do automatyzowania systemu operacyjnego i framework poprawki przez tworzenia obrazów kontenera w chmurze, zapoznaj się z serii samouczek tworzenia ACR trzech części.

> [!div class="nextstepaction"]
> [Tworzenie kontenera obrazów w chmurze z kompilacji rejestru kontenera platformy Azure](container-registry-tutorial-quick-build.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli#log-in-with-a-service-principal

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
