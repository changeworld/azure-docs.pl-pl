---
title: Zarządzanie, rejestrowanie, wdrażanie i monitorowanie modeli uczenia Maszynowego
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak wdrażanie, zarządzanie i monitorowanie swoje modele, aby nieustannie podnosić je za pomocą usługi Azure Machine Learning. Można wdrażać te modele, które uczony przy użyciu usługi Azure Machine Learning, na komputerze lokalnym lub z innych źródeł.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 1/23/2019
ms.custom: seodec18
ms.openlocfilehash: 2cd2d328d33744854bc525e5ecf1dfa3b6e4bcc8
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59275446"
---
# <a name="manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>Zarządzanie, wdrażanie i monitorowanie modeli przy użyciu usługi Azure Machine Learning

W tym artykule możesz Dowiedz się, jak wdrażanie, zarządzanie i monitorowanie swoje modele, aby nieustannie podnosić je za pomocą usługi Azure Machine Learning. Można wdrażać modele, które możesz skonfigurowanych pod kątem korzystania z usługi Azure Machine Learning na komputerze lokalnym lub z innych źródeł. 

Na poniższym diagramie przedstawiono przepływ pracy całego procesu wdrażania: [![Wdrażanie przepływu pracy dla usługi Azure Machine Learning](media/concept-model-management-and-deployment/deployment-pipeline.png)](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

Przepływ pracy wdrożenia obejmuje następujące kroki:
1. **Zarejestruj model** w rejestrze hostowanych w obszarze roboczym usługi Azure Machine Learning
1. **Zarejestruj obrazu** pary, model zawierający skrypt oceniania i zależności w kontenerze przenośny 
1. **Wdrażanie** obraz jako usługi sieci web w chmurze lub na urządzeniach brzegowych
1. **Monitorowanie i zbieranie danych**
1. **Aktualizacja** wdrożenia za pomocą nowego obrazu.

Poszczególne kroki można wykonać niezależnie lub jako część polecenia pojedynczego wdrożenia. Ponadto można zintegrować wdrożenie do **przepływu pracy ciągłej integracji/ciągłego Dostarczania** jak pokazano na poniższej ilustracji.

[!['Usługa Azure Machine Learning ciągłej integracji/ciągłego wdrażania (CI/CD) cyklu "](media/concept-model-management-and-deployment/model-ci-cd.png)](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

## <a name="step-1-register-model"></a>Krok 1: Rejestrowanie modelu

Rejestracja modelu pozwala do przechowywania wersji modeli w chmurze platformy Azure, w obszarze roboczym. Rejestru model ułatwia organizowanie i śledzenie wytrenowane modele.
 
Zarejestrowane modele są identyfikowane przez nazwę i wersję. Zawsze należy zarejestrować model o takiej samej nazwie jak innego istniejącego rejestru zwiększa numer wersji. Możesz także podać dodatkowe metadane tagów podczas rejestracji, który może służyć podczas wyszukiwania dla modeli. Usługa Azure Machine Learning obsługuje każdy model, który może zostać załadowany przy użyciu języka Python 3. 

Nie można usunąć modeli, które są używane przez obraz.

Aby uzyskać więcej informacji, zobacz sekcję modelu rejestru [wdrażanie modeli](how-to-deploy-and-where.md#registermodel).

Na przykład rejestrowania modelu są przechowywane w formacie pakietu pickle, zobacz [samouczka: Szkolenie modeli klasyfikacji obrazów](tutorial-deploy-models-with-aml.md).

Aby uzyskać informacje na temat korzystania z modelami ONNX, zobacz [ONNX i Azure Machine Learning](how-to-build-deploy-onnx.md) dokumentu.

## <a name="step-2-register-image"></a>Krok 2: Obraz do rejestru

Obrazy umożliwiają niezawodny model wdrażania, oraz wszystkie składniki wymagane do używania tego modelu. Obraz zawiera następujące elementy:

* Model
* Aparat oceniania
* Plik oceniania lub aplikacja
* Wszelkie zależności niezbędne do Klasyfikacja modelu

Obraz, który może również obejmować składniki zestawu SDK do rejestrowania i monitorowania. Dane dzienników zestawu SDK może służyć do dostosowania lub ponowne szkolenie modelu, w tym danych wejściowych i wyjściowych modelu.

Usługa Azure Machine Learning obsługuje najbardziej popularnych struktur, ale ogólnie rzecz biorąc dowolnej platformy, które mogą być zainstalowane narzędzie pip może pracować.

Podczas tworzenia obszaru roboczego, więc były inne kilka innych zasobów platformy Azure używane przez tego obszaru roboczego.
Wszystkie obiekty, które są użyte do utworzenia domyślnego obrazu są przechowywane na koncie magazynu platformy Azure, w obszarze roboczym. Możesz podać dodatkowe metadane tagów podczas tworzenia obrazu. Tagi metadanych są także przechowywane przez rejestr obrazów i mogą być wyszukiwane można znaleźć obrazu.

Można również użyć niestandardowych obrazów, które można przekazać do usługi Azure Container Registry i używane przez usługę Azure Machine Learning.

Aby uzyskać więcej informacji, zobacz Konfigurowanie i rejestrowanie części obrazu [wdrażanie modeli](how-to-deploy-and-where.md#configureimage).

## <a name="step-3-deploy-image"></a>Krok 3: Wdrażanie obrazu

W chmurze lub na urządzeniach brzegowych, mogą wdrażać obrazy zarejestrowane. Proces wdrażania tworzy wszystkie zasoby niezbędne do monitorowania, równoważenie obciążenia i automatyczne skalowanie modelu. Dostęp do wdrożonych usług mogą być chronione przy użyciu uwierzytelniania opartego na certyfikatach, zapewniając zasoby zabezpieczeń podczas wdrażania. Możesz również uaktualnić istniejące wdrożenie, aby użyć nowszej obrazu.

Wdrożeń usług internetowych są również można wyszukiwać. Na przykład możesz wyszukać wszystkie wdrożenia określonego modelu lub obrazu.

[![Inferencing elem_docelowe](media/concept-model-management-and-deployment/inferencing-targets.png)](media/concept-model-management-and-deployment/inferencing-targets.png#lightbox)

Obrazy można wdrażać w następujących lokalizacjach docelowych wdrożenia w chmurze:

* Wystąpienie kontenera platformy Azure
* Azure Kubernetes Service
* FPGA maszyn
* Urządzenia w usłudze Azure IoT Edge

Jak usługa zostanie wdrożona, żądanie wnioskowania jest automatycznie równoważeniem obciążenia i klaster będzie skalowany w celu zaspokojenia jakiekolwiek skoki na żądanie. [Mogą być przechwytywane dane telemetryczne o usłudze](how-to-enable-app-insights.md) w usłudze Azure Application Insights skojarzone z obszarem roboczym.

Aby uzyskać więcej informacji, zobacz sekcję wdrażanie [wdrażanie modeli](how-to-deploy-and-where.md#deploy).

## <a name="step-4-monitor-models-and-collect-data"></a>Krok 4: Modele monitorują i zbierają dane

Zestaw SDK do przechwycenia danych i rejestrowania modelu jest dostępna, aby można było monitorować danych wejściowych, danych wyjściowych i inne odpowiednie dane z modelu. Dane są przechowywane jako obiekt blob w ramach konta usługi Azure Storage dla obszaru roboczego.

Aby zestawu SDK za pomocą modelu, należy zaimportować zestawu SDK do oceniania skryptu lub aplikacji. Zestaw SDK umożliwia następnie zaloguj się dane, takie jak parametry, wyniki lub dane wejściowe.

Jeśli zdecydujesz włączyć zbieranie danych modelu za każdym razem, gdy wdrożenie obrazu, szczegółowe informacje niezbędne do przechwycenia danych, takich jak poświadczenia do magazynu osobistej obiektów blob, są automatycznie aprowizowane.

> [!Important]
> Microsoft nie widzi danych zbieranych z modelu. Dane są wysyłane bezpośrednio do konta usługi Azure storage dla obszaru roboczego.

Aby uzyskać więcej informacji, zobacz [jak włączyć zbieranie danych modelu](how-to-enable-data-collection.md).

## <a name="step-5-update-the-deployment"></a>Krok 5. Aktualizuj wdrożenie

Aktualizacje do modelu nie są automatycznie rejestrowane. Podobnie rejestrowanie nowego obrazu nie jest aktualizowane automatycznie wdrożeń, które zostały utworzone z poprzedniej wersji obrazu. Zamiast tego należy ręcznie zarejestrować model, zarejestruj obrazu, a następnie zaktualizuj modelu. Aby uzyskać więcej informacji, zobacz Aktualizowanie sekcji [wdrażanie modeli](how-to-deploy-and-where.md#update).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [jak i gdzie można wdrażać modele](how-to-deploy-and-where.md) za pomocą usługi Azure Machine Learning. Na przykład wdrażania zobacz [samouczka: Wdrażanie modeli klasyfikacji obrazów w usłudze Azure Container Instances](tutorial-deploy-models-with-aml.md).

Dowiedz się, jak tworzyć klienckie aplikacje i usługi, których [korzystanie z modelu wdrożyć jako usługę sieci web](how-to-consume-web-service.md).
