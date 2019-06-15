---
title: JavaScript i strony umowy wersje — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć język JavaScript i używać wersji kontraktu stron w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ef474bec71a9015209b5748b6947816002bd4a5d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511979"
---
# <a name="javascript-and-page-contract-versions-in-azure-active-directory-b2c"></a>Wersje kontraktu JavaScript i stron w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Usługa Azure AD B2C zawiera zbiór zawartość pakietu zawierający elementy interfejsu użytkownika w przepływy użytkownika i zasad niestandardowych HTML, CSS i JavaScript. Aby włączyć język JavaScript dla aplikacji, należy dodać element do Twojej [zasad niestandardowych](active-directory-b2c-overview-custom.md) lub ją włączyć w portalu przepływów użytkownika, zaznacz kontrakt strony i użyj [z usługi b2clogin.com](b2clogin.md) w żądaniach.

Jeśli zamierzasz włączyć [JavaScript](javascript-samples.md) kod po stronie klienta, należy mieć pewność, że elementy Twój kod JavaScript jest tworzony na są niezmienne. W przeciwnym razie zmiany może spowodować nieoczekiwane zachowanie na stronach użytkownika. Aby uniknąć tych problemów, można wymusić użycie kontraktu strony i określ wersję strony umowy. W ten sposób zapewnia, że wszystkie definicje zawartości, które Twój kod JavaScript jest oparty na są niezmienne. Nawet jeśli nie zamierzasz włączyć język JavaScript, możesz określić wersja kontraktu strony dla stron sieci.

## <a name="user-flows"></a>Przepływy użytkowników

W oknie właściwości przepływu użytkownika można włączyć kodu JavaScript, który wymusza także użycie kontraktu strony. Następnie można ustawić wersja kontraktu strony, zgodnie z opisem w następnej sekcji.

![Włącz ustawienie języka JavaScript](media/user-flow-javascript-overview/javascript-settings.png)

Czy JavaScript zostanie włączone we właściwościach przepływ użytkownika, można określić wersja kontraktu strony stron przepływu użytkownika. Otwórz przepływ użytkownika, a następnie wybierz pozycję **układy stron**. W obszarze **Nazwa układu**, wybierz stronę przepływ użytkownika i wybierz **wersja kontraktu strony**.

![Włącz ustawienie języka JavaScript](media/user-flow-javascript-overview/page-contract-version.png)

## <a name="custom-policies"></a>Zasady niestandardowe

Aby włączyć język JavaScript w zasadach niestandardowych, należy dodać **ScriptExecution** elementu **RelyingParty** elementu w pliku zasad niestandardowych. Aby uzyskać więcej informacji, zobacz [przykładowe skrypty JavaScript do użycia w usłudze Azure Active Directory B2C](javascript-samples.md).

Czy należy włączyć język JavaScript w niestandardowe zasady, można określić wersja kontraktu strony dla stron sieci. Aby uzyskać więcej informacji na temat określania kontrakt strony zobacz [zaznacz kontrakt strony w usłudze Azure Active Directory B2C, za pomocą zasad niestandardowych](page-contract.md).

## <a name="next-steps"></a>Kolejne kroki

Zobacz [przykładowe skrypty JavaScript do użycia w usłudze Azure Active Directory B2C](javascript-samples.md).
