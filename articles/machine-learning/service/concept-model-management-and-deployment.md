---
title: Zarządzanie i wdrażanie modeli w usłudze Azure Machine Learning
description: Dowiedz się, jak wdrażanie, zarządzanie i monitorowanie swoje modele, aby nieustannie podnosić je za pomocą usługi Azure Machine Learning. Można wdrażać modele, które możesz skonfigurowanych pod kątem korzystania z usługi Azure Machine Learning na komputerze lokalnym lub z innych źródeł.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: hjerez
ms.author: hjerez
ms.date: 09/24/2018
ms.openlocfilehash: 634f33b6d4ed6e272dfb3d1443b0afc63f822d43
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47055887"
---
# <a name="manage-deploy-and-monitor-models-with-azure-machine-learning"></a>Zarządzanie, wdrażanie i monitorowanie modeli przy użyciu usługi Azure Machine Learning

W tym artykule możesz dowiedzieć się, jak wdrażanie, zarządzanie i monitorowanie swoje modele, aby nieustannie podnosić je za pomocą usługi Azure Machine Learning. Można wdrażać modele, które możesz skonfigurowanych pod kątem korzystania z usługi Azure Machine Learning na komputerze lokalnym lub z innych źródeł. 

[!["Usługa azure Machine Learning ciągłej integracji/ciągłego wdrażania (CI/CD) cyklu"](media/concept-model-management-and-deployment/model-ci-cd.png)](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

## <a name="deployment-workflow"></a>Wdrażanie przepływu pracy

Przepływ pracy wdrożenia polega na: 
1. **Zarejestruj model** w rejestrze hostowanych w obszarze roboczym usługi Azure Machine Learning
1. **Zarejestruj obrazu** pary, model zawierający skrypt oceniania i zależności w kontenerze przenośny 
1. **Wdrażanie** obraz jako usługi sieci web w chmurze lub na urządzeniach brzegowych
1. **Monitorowanie i gromadzenie danych**

Możesz tworzyć każdego kroku, niezależnie od siebie lub jako część polecenia pojedynczego wdrożenia. 

Na poniższym diagramie przedstawiono potok całego procesu wdrażania:

[![Potok ciągłego wdrażania](media/concept-model-management-and-deployment/deployment-pipeline.png)](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

## <a name="step-1-model-registration"></a>Krok 1: Rejestracja modelu

Rejestr modelu przechowuje informacje o wszystkich modeli w obszarze roboczym usługi Azure Machine Learning.
Modele są identyfikowane przez nazwę i wersję. Zawsze należy zarejestrować model o takiej samej nazwie jak innego istniejącego rejestru zwiększa numer wersji. Możesz także podać dodatkowe metadane tagów podczas rejestracji, który może służyć podczas wyszukiwania dla modeli.

Nie można usunąć modeli, które są używane przez obraz.

## <a name="step-2-image-registration"></a>Krok 2: Obraz rejestracji

Obrazy umożliwiają niezawodny model wdrażania, oraz wszystkie składniki wymagane do używania tego modelu. Obraz zawiera następujące elementy:

* Model
* Aparat oceniania
* Plik oceniania lub aplikacja
* Wszelkie zależności niezbędne do Klasyfikacja modelu

Obraz, który może również obejmować składniki zestawu SDK do rejestrowania i monitorowania. Dane dzienników zestawu SDK może służyć do dostosowania lub ponowne szkolenie modelu, w tym danych wejściowych i wyjściowych modelu.

Usługa Azure Machine Learning obsługuje najbardziej popularnych struktur, ale ogólnie rzecz biorąc dowolnej platformy, które mogą być zainstalowane narzędzie pip może pracować.

Podczas tworzenia obszaru roboczego, więc były inne kilka innych zasobów platformy Azure używane przez tego obszaru roboczego.
Wszystkie obiekty, które są używane do tworzenia obrazu są przechowywane na koncie magazynu platformy Azure, w obszarze roboczym. Obraz, który jest tworzony i zapisywany w usłudze Azure Container Registry. Podczas tworzenia obrazu, które są także przechowywane przez rejestr obrazów i może być odpytywana można znaleźć obrazu, możesz podać dodatkowe metadane tagów.

## <a name="step-3-deployment"></a>Krok 3: Wdrażanie

W chmurze lub na urządzeniach brzegowych, mogą wdrażać obrazy zarejestrowane. Proces wdrażania tworzy wszystkie zasoby niezbędne do monitorowania, równoważenie obciążenia i automatycznego skalowania modelu. Dostęp do wdrożonych usług mogą być chronione przy użyciu uwierzytelniania opartego na certyfikatach, zapewniając zasoby zabezpieczeń podczas wdrażania. Możesz również uaktualnić istniejące wdrożenie, aby użyć nowszej obrazu.

Wdrożeń usług internetowych są również można wyszukiwać. Na przykład możesz wyszukać wszystkie wdrożenia określonego modelu lub obrazu.

Obrazy można wdrażać w następujących lokalizacjach docelowych w chmurze:

* Wystąpienie kontenera platformy Azure
* Azure Kubernetes Service
* FPGA maszyn
* Urządzenia w usłudze Azure IoT Edge

[Dowiedz się więcej o którym można wdrożyć](how-to-deploy-and-where.md).

Jak usługa zostanie wdrożona, żądanie wnioskowania jest automatycznie równoważeniem obciążenia i klaster będzie skalowany w celu zaspokojenia jakiekolwiek skoki na żądanie. Dane telemetryczne dotyczące usługi są przechwytywane do usługi Azure Application Insights skojarzone z obszarem roboczym.

## <a name="step-4-monitoring-models-and-data-collection"></a>Krok 4: Modele monitorowania i zbierania danych

Zestaw SDK do przechwycenia danych i rejestrowania modelu jest dostępna, aby można było monitorować danych wejściowych, danych wyjściowych i inne odpowiednie dane z modelu. Dane są przechowywane jako obiekt blob w ramach konta usługi Azure Storage dla obszaru roboczego.

Aby zestawu SDK za pomocą modelu, należy zaimportować zestawu SDK do oceniania skryptu lub aplikacji. Zestaw SDK umożliwia następnie zaloguj się dane, takie jak parametry, wyniki lub dane wejściowe.

Jeśli zdecydujesz się [Włącz zbieranie danych modelu](how-to-enable-data-collection.md) za każdym razem, gdy wdrożono obraz, szczegółowe informacje niezbędne do przechwycenia danych, takich jak poświadczenia do magazynu osobistej obiektów blob, są automatycznie aprowizowane.

> [!Important]
> Microsoft nie widzi danych zbieranych z modelu. Dane są wysyłane bezpośrednio do konta usługi Azure storage dla obszaru roboczego.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [jak i gdzie można wdrażać modele](how-to-deploy-and-where.md) za pomocą usługi Azure Machine Learning.
