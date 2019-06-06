---
title: Dostęp i dostosowywanie nowego portalu dla deweloperów — usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z nowego portalu dla deweloperów w usłudze API Management.
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
ms.openlocfilehash: deb3fdf3069aaad4982d71806c209fe516e3c3d6
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743578"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>Dostęp i Dostosowywanie portalu dla deweloperów w usłudze Azure API Management

W tym artykule przedstawiono sposób korzystania z nowego portalu dla deweloperów usługi Azure API Management. Przeprowadza użytkownika przez proces Edytor wizualny — Dodawanie i edytowanie zawartości — podobnie jak dostosowywanie wyglądu witryny sieci Web.

![Nowy portal dla deweloperów usługi API Management](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
- Importowanie i publikowanie wystąpienia usługi Azure API Management. Aby uzyskać więcej informacji, zobacz [importowanie i publikowanie](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> Nowy portal deweloperów jest obecnie w wersji zapoznawczej.

## <a name="managed-and-self-hosted-versions"></a>Wersje zarządzanych i samodzielnie hostowane

Możesz tworzyć portalu dla deweloperów na dwa sposoby:

- **Zarządzanej wersji** — edytowanie i Dostosowywanie portalu wbudowane wystąpienia usługi API Management i jest dostępny za pośrednictwem adresu URL `<your-api-management-instance-name>.developer.azure-api.net`.
- **Wersja Self-Hosted** — wdrażając i hostingu samodzielnego portalu poza wystąpienia usługi API Management. Takie podejście umożliwia edytowanie portal bazy kodu i rozszerzyć funkcjonalność podana core. Aby uzyskać szczegółowe informacje i instrukcje, zobacz [repozytorium GitHub zawierające kod źródłowy portalu][1].

## <a name="access-the-managed-version-of-the-portal"></a>Dostęp do zarządzanej wersji portalu

Wykonaj poniższe kroki, aby dostęp do zarządzanej wersji portalu.

1. Przejdź do wystąpienia usługi API Management w witrynie Azure portal.
1. Kliknij pozycję **nowego portalu dla deweloperów (wersja zapoznawcza)** przycisku w górnym pasku nawigacyjnym. Zostanie otwarta nowa karta przeglądarki, za pomocą administracyjne wersję portalu. Jeśli po raz pierwszy uzyskujesz dostęp do portalu, domyślnej zawartości zostanie automatycznie aprowizowana.

## <a name="edit-and-customize-the-managed-version-of-the-portal"></a>Edytowanie i dostosowywanie zarządzanej wersji portalu

W trakcie filmu wideo poniżej pokażemy, jak edytować zawartość portalu, dostosowywanie wyglądu witryny sieci Web i publikowania zmian.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat nowego portalu dla deweloperów:

- [Repozytorium GitHub zawierające kod źródłowy][1]
- [Instrukcje dotyczące hostingu samodzielnego portalu][2]
- [Publicznymi planami projektu][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects