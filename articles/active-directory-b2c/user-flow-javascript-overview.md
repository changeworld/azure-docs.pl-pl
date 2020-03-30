---
title: Wersje JavaScript i układu strony
titleSuffix: Azure AD B2C
description: Dowiedz się, jak włączyć obsługę języka JavaScript i używać wersji układu strony w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 23d345ea9f22be5c4dac20e6e8784a8de079bccb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185844"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Wersje języka JavaScript i układu strony w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Usługa Azure AD B2C udostępnia zestaw zawartości pakietowej zawierającej html, CSS i JavaScript dla elementów interfejsu użytkownika w przepływach użytkownika i zasadach niestandardowych.

Aby włączyć obsługę języka JavaScript dla aplikacji:

* Włącz go w przepływie użytkownika przy użyciu portalu Azure
* Wybieranie [układu strony](page-layout.md)
* Użyj [b2clogin.com](b2clogin.md) w swoich żądaniach

Jeśli zamierzasz włączyć kod po stronie klienta [JavaScript,](javascript-samples.md) elementy, na których opierasz javascript, muszą być niezmienne. Jeśli nie są one niezmienne, wszelkie zmiany mogą spowodować nieoczekiwane zachowanie na stronach użytkownika. Aby zapobiec tym problemom, wymuś użycie układu strony i określ wersję układu strony, aby upewnić się, że definicje zawartości, na których opierasz javascript, są niezmienne. Nawet jeśli nie zamierzasz włączać języka JavaScript, możesz określić wersję układu strony dla stron.

## <a name="enable-javascript"></a>Włączanie języka JavaScript

We **właściwościach**przepływu użytkownika można włączyć obsługę języka JavaScript. Włączenie języka JavaScript wymusza również użycie układu strony. Następnie można ustawić wersję układu strony dla przepływu użytkownika, jak opisano w następnej sekcji.

![Strona właściwości przepływu użytkownika z wyróżnionym ustawieniem Włącz javascript](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>Wybieranie wersji układu strony

Niezależnie od tego, czy w płynach przepływu użytkownika włączono obsługę języka JavaScript, można określić wersję układu strony dla stron przepływu użytkownika. Otwórz przepływ użytkownika i wybierz **pozycję Układy stron**. W obszarze **NAZWA UKŁADU**wybierz stronę przepływu użytkownika i wybierz wersję **układu strony**.

Aby uzyskać informacje o różnych wersjach układu strony, zobacz [dziennik zmian wersji układu strony](page-layout.md).

![Ustawienia układu strony w portalu z listy rozwijanej wersja układu strony](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>Następne kroki

Przykłady użycia języka JavaScript można znaleźć w [przykładach języka JavaScript do użycia w usłudze Azure Active Directory B2C](javascript-samples.md).
