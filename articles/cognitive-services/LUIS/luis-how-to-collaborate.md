---
title: Współpraca z innych współpracowników LUIS aplikacji na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak współpraca z innych uczestników w aplikacjach opis języka (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/05/2018
ms.author: v-geberr
ms.openlocfilehash: c0451f7621a3c18dbf365f3a03934924c030092f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348956"
---
# <a name="collaborate-with-others-on-language-understanding-luis-apps"></a>Współpracę z innymi osobami w aplikacjach opis języka (LUIS)  

Umożliwia współpracę z innymi osobami w aplikacji LUIS razem. 

## <a name="owner-and-collaborators"></a>Właściciel i współpracowników
Aplikacja ma jednego właściciela, ale może mieć wiele współpracowników. 

## <a name="add-collaborator"></a>Dodawanie współpracownika

Aby umożliwić współpracownikom edytować aplikację LUIS na **ustawienia** strony LUIS aplikacji, wprowadź adres e-mail współpracownika i kliknij przycisk **współpracownika Dodaj**.

![Dodawanie współpracownika](./media/luis-how-to-collaborate/add-collaborator.png)

* Współpracownicy zalogowanie się i edytowanie aplikacji LUIS w tym samym czasie, w którym pracujesz w aplikacji. <!--If a collaborator edits the LUIS app, you see a notification at the top of the browser.-->
* Współpracownicy nie można dodać inne współpracowników.

## <a name="set-application-as-public"></a>Zestaw aplikacji jako publiczny
Zobacz [dostępu do punktu końcowego aplikacji publicznych](luis-concept-security.md#public-app-endpoint-access) Aby uzyskać więcej informacji.

### <a name="transfer-of-ownership"></a>Przeniesienie własności
Podczas LUIS nie obsługuje obecnie przeniesienie własności, możesz wyeksportować aplikację, a inny użytkownik LUIS można zaimportować aplikację. Może występować niewielkie różnice w wyniki LUIS między dwiema aplikacjami. 
