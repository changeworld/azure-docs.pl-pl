---
title: Wersje języka JavaScript i układu strony — Azure Active Directory B2C | Microsoft Docs
description: Informacje o sposobie włączania języka JavaScript i używania wersji układu strony w programie Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0eb5c89387d8bdcf0e0b72c669c42f716ff5fbb3
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227101"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Wersje języka JavaScript i układu strony w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C zawiera zestaw spakowanych zawartości zawierający kod HTML, CSS i JavaScript dla elementów interfejsu użytkownika w przepływach użytkownika i zasadach niestandardowych. Aby włączyć obsługę języka JavaScript dla aplikacji, należy dodać element do [zasad niestandardowych](active-directory-b2c-overview-custom.md) lub włączyć go w portalu dla przepływów użytkowników, wybrać układ strony i użyć [b2clogin.com](b2clogin.md) w swoich żądaniach.

Jeśli zamierzasz włączyć kod po stronie klienta [JavaScript](javascript-samples.md) , upewnij się, że elementy, które są na pewno są używane w języku JavaScript, są niezmienne. W przeciwnym razie wszelkie zmiany mogą spowodować nieoczekiwane zachowanie na stronach użytkownika. Aby uniknąć tych problemów, można wymusić użycie układu strony i określić wersję układu strony. To gwarantuje, że wszystkie definicje zawartości, na których oparto kod JavaScript, są niezmienne. Nawet jeśli nie zamierzasz włączać języka JavaScript, możesz określić wersję układu strony dla stron.

## <a name="user-flows"></a>Przepływy użytkowników

We właściwościach przepływu użytkownika można włączyć język JavaScript, który również wymusza użycie układu strony. Następnie można ustawić wersję układu strony dla przepływu użytkownika zgodnie z opisem w następnej sekcji.

![Strona właściwości przepływu użytkownika z wyróżnionym ustawieniem Włącz język JavaScript](media/user-flow-javascript-overview/javascript-settings.png)

### <a name="select-a-page-layout-version"></a>Wybierz wersję układu strony

Niezależnie od tego, czy w obszarze właściwości przepływu użytkownika jest włączone JavaScript, możesz określić wersję układu strony dla stron przepływu użytkownika. Otwórz przepływ użytkownika i wybierz pozycję **układy strony**. W obszarze **Nazwa układu**wybierz stronę przepływ użytkownika i wybierz **wersję układu strony**.

Informacje o różnych wersjach układów stron można znaleźć w [dzienniku zmian wersji](page-layout.md#version-change-log).

![Ustawienia układu strony w portalu pokazujące listę rozwijaną wersji układu strony](media/user-flow-javascript-overview/page-layout-version.png)

## <a name="custom-policies"></a>Zasady niestandardowe

Aby włączyć język JavaScript w zasadach niestandardowych, należy dodać element **ScriptExecution** do elementu **RelyingParty** w pliku zasad niestandardowych. Aby uzyskać więcej informacji, zobacz [przykłady JavaScript do użycia w Azure Active Directory B2C](javascript-samples.md).

Niezależnie od tego, czy w zasadach niestandardowych włączono obsługę języka JavaScript, możesz określić wersję układu strony dla stron. Aby uzyskać więcej informacji na temat określania układu strony, zobacz [Wybieranie układu strony w Azure Active Directory B2C przy użyciu zasad niestandardowych](page-layout.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje o różnych wersjach układu strony, zobacz sekcję **Dziennik zmian wersji** w temacie [Wybieranie układu strony w Azure Active Directory B2C przy użyciu zasad niestandardowych](page-layout.md#version-change-log).

Przykłady użycia języka JavaScript można znaleźć w przykładach [języka JavaScript do użycia w Azure Active Directory B2C](javascript-samples.md).
