---
title: Znajdowanie interfejsu API dla aplikacji opracowanej na zamówienie | Azure
description: Jak skonfigurować uprawnienia potrzebne do uzyskania dostępu do określonego interfejsu API w niestandardowej opracowanej aplikacji usługi Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.openlocfilehash: bc50ec86866b7fe04c549c7fd463b6de4df3444b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76698394"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Jak znaleźć określony interfejs API potrzebny dla aplikacji opracowanej na zamówienie

Dostęp do interfejsów API wymaga konfiguracji zakresów dostępu i ról. Jeśli chcesz udostępnić interfejsy API sieci web aplikacji zasobów do aplikacji klienckich, należy skonfigurować zakresy dostępu i role dla interfejsu API. Jeśli chcesz, aby aplikacja kliencka uzyskała dostęp do internetowego interfejsu API, musisz skonfigurować uprawnienia dostępu do interfejsu API w rejestracji aplikacji.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurowanie aplikacji zasobów na potrzeby uwidoczniania internetowych interfejsów API

Podczas wystawiania interfejsu API sieci web interfejs API być wyświetlane na liście **Wybierz interfejs API** podczas dodawania uprawnień do rejestracji aplikacji. Aby dodać zakresy dostępu, wykonaj kroki opisane w [temacie Konfigurowanie aplikacji w celu udostępnienia interfejsów API sieci Web](quickstart-configure-app-expose-web-apis.md).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Konfigurowanie aplikacji klienckiej w celu uzyskania dostępu do internetowych interfejsów API

Po dodaniu uprawnień do rejestracji aplikacji można **dodać dostęp do interfejsu API** do udostępnianych interfejsów API sieci Web. Aby uzyskać dostęp do internetowych interfejsów API, wykonaj kroki opisane w [temacie Konfigurowanie aplikacji klienckiej](quickstart-configure-app-access-web-apis.md)w celu uzyskania dostępu do internetowych interfejsów API .

## <a name="next-steps"></a>Następne kroki

- [Opis manifestu aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)
