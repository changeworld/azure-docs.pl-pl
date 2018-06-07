---
title: Jak anulowanie zastrzeżenia urządzeń, które zostały udostępnione przy użyciu usługi inicjowania obsługi urządzeń Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Jak deprovision urządzeniami, które zostały udostępnione przy użyciu usługi inicjowania obsługi urządzeń Centrum IoT Azure
author: bryanla
ms.author: bryanla
ms.date: 05/11/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 56d9c4c057e226f6cd93551380436cdc52d79169
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34630361"
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>Jak anulowanie zastrzeżenia urządzenia, które wcześniej były udostępniane do automatycznego 

Może być konieczne deprovision urządzeń, które były wcześniej automatycznie — za pośrednictwem usługi inicjowania obsługi urządzeń. Na przykład urządzenie może być sprzedaży lub przenieść do innego Centrum IoT lub może być utracone, skradzione lub w przeciwnym razie złamania zabezpieczeń. 

Ogólnie rzecz biorąc anulowania obsługi urządzenia wymaga wykonania dwóch kroków:

1. Disenroll urządzenia z usługi inicjowania obsługi administracyjnej, aby uniknąć przyszłych automatycznego inicjowania obsługi. W zależności od tego, czy chcesz odwołać dostęp tymczasowo lub na stałe można wyłączyć lub usunąć wpisu rejestracji. W przypadku urządzeń korzystających z zaświadczania X.509 warto zdezaktywuj lub usuń wpis w hierarchii Twoich istniejących grup rejestracji.  
 
   - Aby dowiedzieć się, jak disenroll urządzenia, zobacz [jak urządzenia z usługi udostępniania urządzenia Centrum IoT Azure disenroll](how-to-revoke-device-access-portal.md).
   - Aby dowiedzieć się, jak disenroll urządzenia programowo przy użyciu jednej z inicjowania obsługi administracyjnej zestawów SDK usługi, zobacz [Zarządzanie rejestracji urządzeń z usługi SDK](how-to-manage-enrollments-sdks.md).

2. Wyrejestrowania urządzenia z Centrum IoT, aby uniknąć przyszłych transmisji danych i łączności. Ponownie można tymczasowo wyłączyć lub trwale usunąć urządzenia wpis w rejestrze tożsamości, dla której zainicjowano Centrum IoT. Zobacz [Wyłącz urządzenia](/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) Aby dowiedzieć się więcej na temat niepełnosprawność. Zobacz "Urządzenia zarządzania / urządzenia IoT" dla zasobu Centrum IoT w [portalu Azure](https://portal.azure.com).

Kolejnych kroków, które należy wykonać w celu anulowanie zastrzeżenia urządzenia są zależne od jego mechanizmu zaświadczania i jego wpis rejestracji mające zastosowanie do inicjowania obsługi administracyjnej usługi. W poniższych sekcjach omówiono proces, na podstawie typu rejestracji i zaświadczania.

## <a name="individual-enrollments"></a>Poszczególne rejestracji
Urządzenia używające zaświadczenia modułu TPM lub zaświadczania X.509 przy użyciu certyfikatu liścia za pośrednictwem wpis poszczególnych rejestracji. 

Aby anulowanie zastrzeżenia urządzenie ma poszczególnych rejestracji: 

1. Disenroll urządzenia z usługą inicjowania obsługi administracyjnej:

   - W przypadku urządzeń korzystających z modułem TPM zaświadczania usunięcie wpisu rejestracji poszczególnych trwale odwołać dostęp urządzenia do inicjowania obsługi usługi lub wyłączyć wpis tymczasowo odwołać dostęp do niej. 
   - W przypadku urządzeń korzystających z zaświadczania X.509 można usunąć lub wyłączyć wpis. Należy jednak pamiętać, jeśli usuniesz poszczególnych rejestracji dla urządzenia, których używa X.509 i istnieje grupa włączone rejestracji certyfikatu podpisywania, w tym urządzenia łańcucha certyfikatów, urządzenia mogą ponownie zarejestrować. W przypadku takich urządzeń może być bezpieczniejsze wyłączyć wpisu rejestracji. Ale uniemożliwi urządzenia z ponownego zarejestrowania, niezależnie od tego, czy istnieje grupa rejestracji włączone dla jednego z jego certyfikatów podpisywania.

2. Wyłączenie lub usunięcie urządzenia w rejestrze tożsamości udostępniony do Centrum IoT. 


## <a name="enrollment-groups"></a>Grupy rejestracji
Z zaświadczania X.509 można też udostępnić za pośrednictwem grupy usługi rejestracji urządzeń. Grupy rejestracji są skonfigurowane przy użyciu certyfikatu podpisywania, albo pośredni lub główny certyfikat urzędu certyfikacji i kontroli dostępu do inicjowania obsługi usługi dla urządzeń z tego certyfikatu w łańcuchu swoich certyfikatów. Aby dowiedzieć się więcej o grupach rejestracji i certyfikaty X.509 z usługą udostępniania, zobacz [certyfikatów X.509](concepts-security.md#x509-certificates). 

Aby wyświetlić listę urządzeń, które zostały udostępnione przez grupę rejestracji, można wyświetlić szczegóły grupy rejestracji. Jest to prosty sposób zrozumieć Centrum IoT, którego zainicjowano poszczególnych urządzeń do. Aby wyświetlić listę urządzeń: 

1. Zaloguj się do portalu Azure, a następnie kliknij przycisk **wszystkie zasoby** w menu po lewej stronie.
2. Kliknij przycisk inicjowania obsługi usługi na liście zasobów.
3. W usłudze udostępniania kliknij **Zarządzanie rejestracji**, a następnie wybierz pozycję **grup rejestracji** kartę.
4. Kliknij grupę rejestracji, aby go otworzyć.

   ![Wyświetl wpis grupy rejestracji w portalu](./media/how-to-unprovision-devices/view-enrollment-group.png)

Za pomocą grup rejestracji istnieją dwa scenariusze, które należy uwzględnić:

- Aby anulowanie zastrzeżenia wszystkich urządzeń, które zostały udostępnione przez grupę rejestracji:
  1. Wyłącz grupie rejestracji, aby wyeliminować swojego certyfikatu podpisywania. 
  2. Użyj listy elastycznie urządzeń dla tej grupy rejestracji, aby wyłączyć lub usunąć poszczególne urządzenia z rejestru tożsamości jego odpowiednich Centrum IoT. 
  3. Po wyłączeniu lub usunięciu wszystkich urządzeń z ich odpowiednich centra IoT, opcjonalnie można usunąć grupy rejestracji. Należy jednak pamiętać, że po usunięciu grupy rejestracji i istnieje grupa włączone rejestracji certyfikatu podpisywania się wyżej w łańcuchu certyfikatów co najmniej jednego z urządzeń, te urządzenia mogą ponownie zarejestrować. 

- Aby anulowanie zastrzeżenia jednego urządzenia z grupy rejestracji:
  1. Utwórz wyłączone rejestracji poszczególnych dla swojego certyfikatu liścia (urządzenia). Podczas nadal pozwalające na dostęp do innych urządzeń, które mają grupy rejestracji certyfikatu podpisywania w łańcuchu ich to odwołuje dostęp do inicjowania obsługi usługi dla tego urządzenia. Nie należy usuwać wyłączone poszczególnych rejestracji dla urządzenia. Takie postępowanie umożliwi urządzenia ponownie zarejestrować za pomocą grupy rejestracji. 
  2. Zapoznaj się z listą elastycznie urządzeń dla tej grupy rejestracji można znaleźć Centrum IoT, które urządzenia została przygotowana do i Wyłącz lub usuń go z tego koncentratora rejestru tożsamości. 
  
  










