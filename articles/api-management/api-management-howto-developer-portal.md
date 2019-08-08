---
title: Dostęp i dostosowywanie nowego portalu dla deweloperów — API Management platformy Azure | Microsoft Docs
description: Dowiedz się, jak korzystać z nowego portalu dla deweloperów w API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2019
ms.author: apimpm
ms.openlocfilehash: 27d5dcc99db040036ba296911aa33d8a312bb23f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851506"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>Dostęp do nowego portalu dla deweloperów w usłudze Azure API Management i dostosowywanie go

W tym artykule pokazano, jak uzyskać dostęp do nowego portalu dla deweloperów platformy Azure API Management. Przeprowadzi Cię przez środowisko edytora wizualnego — Dodawanie i Edytowanie zawartości oraz Dostosowywanie wyglądu witryny sieci Web.

![Nowy portal API Management dla deweloperów](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a> Wymagania wstępne

- Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
- Importowanie i publikowanie wystąpienia usługi Azure API Management. Aby uzyskać więcej informacji, zobacz [Importowanie i publikowanie](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> Nowy portal dla deweloperów jest obecnie w wersji zapoznawczej.

## <a name="managed-vs-self-hosted"></a>Zarządzane i samodzielne wersje

Portal dla deweloperów można skompilować na dwa sposoby:

- **Wersja zarządzana** — edytując i dostosowując Portal, który jest wbudowany w wystąpienie API Management i jest dostępny za pomocą adresu URL `<your-api-management-instance-name>.developer.azure-api.net`.
- **Samodzielna wersja** — przez wdrożenie i samodzielne hostowanie portalu poza wystąpieniem API Management. Takie podejście umożliwia edytowanie bazy kodu portalu i poszerzanie podanych funkcji podstawowych. Aby uzyskać szczegółowe informacje i instrukcje, zapoznaj się z [repozytorium GitHub za pomocą kodu źródłowego portalu][1].

## <a name="managed-access"></a>Dostęp do zarządzanej wersji portalu

Wykonaj poniższe kroki, aby uzyskać dostęp do zarządzanej wersji portalu.

1. Przejdź do wystąpienia usługi API Management w Azure Portal.
1. Kliknij przycisk **Nowy portal dla deweloperów (wersja zapoznawcza)** na górnym pasku nawigacyjnym. Zostanie otwarta nowa karta przeglądarki z wersją administracyjną portalu. Jeśli po raz pierwszy uzyskujesz dostęp do portalu, domyślna zawartość zostanie automatycznie zainicjowana.

## <a name="managed-tutorial"></a>Edytowanie i dostosowywanie zarządzanej wersji portalu

W poniższym filmie wideo pokazano, jak edytować zawartość portalu, dostosować wygląd witryny sieci Web i opublikować zmiany.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="faq"></a>Często zadawane pytania

W tej sekcji udzielamy odpowiedzi na często zadawane pytania dotyczące nowego portalu dla deweloperów, który ma charakter ogólny. Pytania specyficzne dla własnej wersji hostowanej można znaleźć w [sekcji wiki repozytorium GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-content-from-the-old-developer-portal-to-the-new-one"></a>Jak przeprowadzić migrację zawartości ze starego portalu dla deweloperów do nowego?

Nie możesz. Portale nie są zgodne.

### <a name="when-will-the-portal-become-generally-available"></a>Kiedy portal stanie się ogólnie dostępny?

Portal jest obecnie w wersji zapoznawczej i stanie się ogólnie dostępny do końca roku kalendarzowego (2019). Wersja zapoznawcza nie powinna być używana do celów produkcyjnych.

### <a name="will-the-old-portal-be-deprecated"></a>Czy stary Portal będzie przestarzały?

Tak, będzie ona przestarzała po ogólnym udostępnieniu nowej. Jeśli masz problemy, zgłoś je [w ramach dedykowanego problemu usługi GitHub](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>Czy nowy portal ma wszystkie funkcje starego portalu?

Celem ogólnie dostępnej jest zapewnienie wysokiej dostępności funkcji opartej na scenariuszu w starym portalu. Do tego czasu wersja zapoznawcza może nie mieć wdrożonych wybranych funkcji.

Wyjątkami są *aplikacje* i *problemy* ze starego portalu, które nie będą dostępne w nowym portalu. Jeśli w starym portalu są używane *problemy* , które są potrzebne, należy opublikować komentarz w [ramach dedykowanego problemu](https://github.com/Azure/api-management-developer-portal/issues/122)z usługą GitHub.

### <a name="ive-found-bugs-andor-id-like-to-request-a-feature"></a>Znaleźliśmy błędy i/lub chcę zażądać funkcji.

Wspaniale! Możesz przekazać nam swoją opinię, przesłać żądanie funkcji lub zgłosić raport o usterce w [sekcji problemy dotyczące repozytorium GitHub](https://github.com/Azure/api-management-developer-portal/issues). W tym miejscu będziemy również wdzięczni za opinie dotyczące problemów oznaczonych `community` etykietą.

### <a name="i-want-to-move-the-content-of-the-new-portal-between-environments-how-can-i-do-that-and-do-i-need-to-go-with-the-self-hosted-version"></a>Chcę przenieść zawartość nowego portalu między środowiskami. Jak można to zrobić, aby móc przejść do samodzielnej wersji?

Można to zrobić w obu wersjach portalu — zarządzanych i samodzielnych. Nowy portal dla deweloperów obsługuje wyodrębnianie zawartości za pomocą interfejsu API zarządzania usługi API Management. Interfejsy API są udokumentowane [w sekcji wiki repozytorium GitHub](https://github.com/Azure/api-management-developer-portal/wiki/). Zapisano również [skrypt](https://github.com/Azure/api-management-developer-portal/blob/master/scripts/migrate.bat), który może pomóc Ci rozpocząć pracę.

Nadal pracujemy nad wyrównaniam tego procesu za pomocą zestawu API Management DevOps Resource Kit.

### <a name="how-can-i-select-a-layout-when-creating-a-new-page"></a>Jak mogę wybrać *Układ* podczas tworzenia nowej *strony*?

*Układ* zostanie zastosowany do strony, dopasowując jej szablon adresu URL do adresu URL *strony* . Na przykład *Układ* z `/wiki/*` szablonem adresu URL zostanie zastosowany do `/wiki/` każdej *strony* z segmentem: `/wiki/getting-started`, `/wiki/styles`i tak dalej.

### <a name="why-doesnt-the-interactive-developer-console-work"></a>Dlaczego interaktywna konsola dewelopera nie działa?

Prawdopodobnie jest on związany z mechanizmem CORS. Konsola interaktywna wykonuje żądanie interfejsu API po stronie klienta z przeglądarki. Problem CORS można rozwiązać, dodając [zasady CORS](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS) w interfejsach API. Możesz określić wszystkie parametry ręcznie (na przykład źródło https://contoso.com) lub użyć wartości wieloznacznej `*` ).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o nowym portalu dla deweloperów:

- [Repozytorium GitHub z kodem źródłowym][1]
- [Instrukcje dotyczące samoobsługowego udostępniania portalu][2]
- [Publiczny plan projektu][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects