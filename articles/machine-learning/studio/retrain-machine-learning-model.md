---
title: Ponownie ucz modelu uczenia maszynowego | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ponownie ucz modelu i usługi sieci Web, aby użyć nowo uczonego modelu w usłudze Azure Machine Learning aktualizacji.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: d1cb6088-4f7c-4c32-94f2-f7523dad9059
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: ca7ad5a46c1401a283879f8aba80c781a88fc089
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835433"
---
# <a name="retrain-a-machine-learning-model"></a>Ponownie ucz modelu uczenia maszynowego
W ramach procesu operationalization modeli uczenia maszyny w usłudze Azure Machine Learning modelu są uczone i zapisać. Następnie należy go utworzyć predicative usługi sieci Web. Usługi sieci Web mogą być następnie używane w witrynach sieci web, pulpity nawigacyjne i aplikacji mobilnych. 

Modele utworzone za pomocą uczenia maszynowego zwykle nie są statyczne. Wraz ze wzrostem dostępności nowych danych lub gdy konsumenta interfejsu API ma własne dane modelu musi być retrained. 

Ponownego trenowania może występować często. Z funkcją programowe ponownego trenowania interfejsu API można programowo retrain modelu przy użyciu interfejsów API ponownego trenowania i aktualizować usługi sieci Web z nowo trenowanego modelu. 

Tym dokumencie opisano proces ponownego trenowania i przedstawiono sposób użycia interfejsów API ponownego trenowania.

## <a name="why-retrain-defining-the-problem"></a>Dlaczego ponownie ucz: Definiowanie problemu
W ramach procesu szkolenia uczenia maszynowego model jest uczony przy użyciu zestawu danych. Modele utworzone za pomocą uczenia maszynowego zwykle nie są statyczne. Wraz ze wzrostem dostępności nowych danych lub gdy konsumenta interfejsu API ma własne dane modelu musi być retrained.

W tych scenariuszach Programistyczny interfejs API udostępnia wygodny sposób pozwalającej użytkownikowi lub konsumenta swoje interfejsy API do tworzenia klienta, który może na podstawie jednorazowe lub regularne retrain modelu przy użyciu własnych danych. Następnie mogą oceniać wyniki ponownego trenowania i aktualizacji interfejsu API usługi sieci Web, aby użyć nowo trenowanego modelu.

> [!NOTE]
> Jeśli masz istniejące eksperyment uczenia i usługi sieci Web nowego można wyewidencjonować ponownego próbkowania istniejącej usługi sieci Web predykcyjnej zamiast następujące wskazówki wymienionych w poniższej sekcji.
> 
> 

## <a name="end-to-end-workflow"></a>Kompletny przepływ pracy
Ten proces obejmuje następujące składniki: A eksperyment uczenia i eksperyment predykcyjny publikowane jako usługę sieci Web. Aby włączyć ponownego trenowania trenowanego modelu, eksperyment uczenia musi zostać opublikowany jako usługę sieci Web, przy czym dane wyjściowe trenowanego modelu. Dzięki temu dostęp API do modelu do ponownego trenowania. 

Poniższe kroki dotyczą zarówno nowe, jak i klasycznych sieci Web usług:

Tworzenie początkowej usługi sieci Web predykcyjnej:

* Tworzenie eksperymentu szkolenia
* Tworzenie eksperymentu predykcyjnej sieci web
* Wdrażanie usługi sieci web predykcyjnej

Ponownie ucz usługi sieci Web:

* Eksperyment uczenia aktualizacji, aby umożliwić ponownego trenowania
* Wdrażanie ponownego trenowania usługi sieci web
* Ponownie ucz modelu za pomocą kodu usługi wykonywania wsadowego

Aby uzyskać wskazówki poprzednich kroków, zobacz [Retrain Machine Learning programowo modele](retrain-models-programmatically.md).

> [!NOTE] 
> Aby wdrożyć nową usługę sieci web musi masz wystarczające uprawnienia do subskrypcji, do którego należy wdrożyć usługę sieci web. Aby uzyskać więcej informacji, zobacz [zarządzania usługi sieci Web przy użyciu portalu usługi sieci Web systemu Azure Machine Learning](manage-new-webservice.md). 

Jeśli wdrożono klasycznym usługi sieci Web:

* Utwórz nowy punkt końcowy usługi sieci Web predykcyjnej
* Pobierz adres URL poprawki i kod
* Użyj adresu URL PATCH, aby wskazywały nowy punkt końcowy na retrained modelu 

Aby uzyskać wskazówki poprzednich kroków, zobacz [Retrain usługi sieci Web klasycznego](retrain-a-classic-web-service.md).

Jeśli wystąpiły problemy podczas ponownego trenowania usługi sieci Web klasycznego, zobacz [Rozwiązywanie problemów z ponownego trenowania usługi sieci Web Azure Machine Learning klasycznego](troubleshooting-retraining-models.md).

Jeśli wdrożono usługę sieci Web nowe:

* Zaloguj się do konta usługi Azure Resource Manager
* Pobierz definicję usługi sieci Web
* Eksportowanie definicji usługi sieci Web w formacie JSON
* Aktualizacja odwołania do `ilearner` obiektu blob w formacie JSON
* Zaimportuj dane JSON do definicji usługi sieci Web
* Aktualizacja usługi sieci Web z nowego definicji usługi sieci Web

Aby uzyskać wskazówki poprzednich kroków, zobacz [Retrain usługi nowej sieci Web przy użyciu poleceń cmdlet programu PowerShell do zarządzania Machine Learning](retrain-new-web-service-using-powershell.md).

Proces konfigurowania ponownego trenowania dla usługi sieci Web klasycznego obejmuje następujące kroki:

![Omówienie procesu ponownego trenowania][1]

Proces konfigurowania ponownego trenowania dla usługi sieci Web nowego obejmuje następujące kroki:

![Omówienie procesu ponownego trenowania][7]

## <a name="other-resources"></a>Inne zasoby
* [Modele ponownego trenowania i aktualizowania usługi Azure Machine Learning z fabryką danych Azure](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/)
* [Tworzenie wielu modeli uczenia maszynowego i sieci web punktów końcowych usługi z doświadczenia przy użyciu programu PowerShell](create-models-and-endpoints-with-powershell.md)
* [AML ponownego trenowania modeli przy użyciu interfejsów API](https://www.youtube.com/watch?v=wwjglA8xllg) wideo pokazuje, jak ponownie ucz modele uczenia maszynowego utworzone w usłudze Azure Machine Learning przy użyciu ponownego trenowania interfejsów API i programu PowerShell.

<!--image links-->
[1]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png

