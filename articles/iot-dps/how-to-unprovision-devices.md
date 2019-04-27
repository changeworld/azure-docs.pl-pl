---
title: Jak anulować aprowizację urządzeń, które zostały udostępnione przy użyciu usługi Azure IoT Hub Device Provisioning | Dokumentacja firmy Microsoft
description: Jak anulować aprowizację urządzeń, które zostały udostępnione przy użyciu usługi Azure IoT Hub Device Provisioning Service
author: wesmc7777
ms.author: wesmc
ms.date: 05/11/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: ba62d8cff646ce5ef4f4b8a36fdad78ddc354227
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626533"
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>Jak anulować aprowizację urządzeń, które wcześniej zostały udostępnione do automatycznego 

Może okazać się niezbędne do anulowania aprowizacji urządzeń, które były wcześniej automatycznie aprowizowane za pośrednictwem usługi Device Provisioning Service. Na przykład urządzenie może być sprzedawana lub przeniesiony do innej usługi IoT hub lub może być utraty, kradzieży lub złamania w inny sposób. 

Ogólnie rzecz biorąc anulowanie aprowizacji urządzenia obejmuje dwa kroki:

1. Disenroll urządzenia z Twoją usługą aprowizacji, aby uniemożliwić przyszłe automatycznej aprowizacji. W zależności od tego, czy chcesz odwołać dostęp, tymczasowo lub trwale można wyłączyć lub usunąć wpis rejestracji. W przypadku urządzeń, które używają zaświadczenia X.509 warto zdezaktywuj lub usuń wpis w hierarchii Twoich istniejących grup rejestracji.  
 
   - Aby dowiedzieć się, jak disenroll urządzenia, zobacz [jak disenroll urządzenia z usługi Azure IoT Hub Device Provisioning Service](how-to-revoke-device-access-portal.md).
   - Aby dowiedzieć się, jak disenroll urządzenia programowo przy użyciu jednego z inicjowania obsługi administracyjnej zestawów SDK usługi, zobacz [Zarządzanie rejestracjami urządzeń przy użyciu zestawów SDK usługi](how-to-manage-enrollments-sdks.md).

2. Wyrejestrować urządzenie z usługi IoT Hub, aby uniemożliwić przyszłe transmisji danych i komunikacji. Ponownie możesz tymczasowo wyłączyć lub trwale usunąć wpis urządzenia w rejestrze tożsamości Centrum IoT, w której została aprowizowana. Zobacz [wyłączanie urządzeń](/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) Aby dowiedzieć się więcej na temat niepełnosprawność. Zobacz "Urządzenia zarządzania / urządzenia IoT" dla zasobu usługi IoT Hub w [witryny Azure portal](https://portal.azure.com).

Dokładnych kroków, które należy wykonać, aby anulować aprowizację urządzenia, zależy od jego mechanizm zaświadczania i jego wpis dotyczy rejestracji z Twoją usługą aprowizacji. Omówienie procesu, w zależności od typu rejestracji i zaświadczania można znaleźć w poniższych sekcjach.

## <a name="individual-enrollments"></a>Rejestracje indywidualne
Urządzenia używające zaświadczenia modułu TPM lub zaświadczenia X.509 przy użyciu certyfikatu liścia są aprowizowane za pośrednictwem wpis rejestracji indywidualnej. 

Aby anulować aprowizację urządzeniu wyposażonym w rejestracji indywidualnej: 

1. Disenroll z Twoją usługą aprowizacji urządzenia:

   - W przypadku urządzeń, które używania zaświadczeń modułu TPM Usuń wpis rejestracji indywidualnej trwale odwołać dostęp urządzenia do usługi aprowizacji lub wyłączyć wpis, aby tymczasowo odwołać dostępu. 
   - W przypadku urządzeń, które używają zaświadczenia X.509 możesz usunąć lub wyłączyć wpis. Należy jednak pamiętać, jeśli usuniesz rejestrację indywidualną dla urządzenia, która używa X.509 i grupę rejestracji włączone istnieje dla certyfikatu podpisywania, w tym, że urządzenia łańcucha certyfikatów, urządzenie ponownie zarejestrować. Dla takich urządzeń może być bezpieczniejsze Wyłącz wpis rejestracji. Ale uniemożliwi urządzenia z rejestracji, niezależnie od tego, czy istnieje grupę rejestracji włączone dla jednego z jego certyfikatów podpisywania.

2. Wyłączanie lub usuwanie urządzenia w rejestrze tożsamości Centrum IoT, które zostało zaaprowizowane do. 


## <a name="enrollment-groups"></a>Grupy rejestracji
W przypadku zaświadczania X.509 urządzeń mogą być udostępniane za pośrednictwem grupy rejestracji. Grupy rejestracji są skonfigurowane przy użyciu certyfikatu podpisywania, albo pośredni lub główny certyfikat urzędu certyfikacji i kontrolować dostęp do usługi aprowizacji urządzeń przy użyciu tego certyfikatu w swoim łańcuchu certyfikatów. Aby dowiedzieć się więcej na temat grup rejestracji i certyfikaty X.509 z usługą aprowizacji, zobacz [certyfikaty X.509](concepts-security.md#x509-certificates). 

Aby wyświetlić listę urządzeń, które zostały udostępnione za pośrednictwem grupy rejestracji, możesz wyświetlić szczegóły grupy rejestracji. Jest to prosty sposób, aby zrozumieć, który został aprowizowany do każdego urządzenia usługi IoT hub. Aby wyświetlić listę urządzeń: 

1. Zaloguj się do witryny Azure portal, a następnie kliknij przycisk **wszystkie zasoby** w menu po lewej stronie.
2. Kliknij przycisk z Twoją usługą aprowizacji, na liście zasobów.
3. W usłudze aprowizacji kliknij **Zarządzanie rejestracjami**, a następnie wybierz **grup rejestracji** kartę.
4. Kliknij grupę rejestracji, aby go otworzyć.

   ![Wyświetl wpis grupy rejestracji w portalu](./media/how-to-unprovision-devices/view-enrollment-group.png)

Z grupami rejestracji istnieją dwa scenariusze, aby wziąć pod uwagę:

- Aby anulować aprowizację wszystkich urządzeń, które zostały udostępnione za pośrednictwem grupy rejestracji:
  1. Wyłącz grupę rejestracji do czarnej listy swojego certyfikatu podpisywania. 
  2. Użyj listy aprowizowanych urządzeń dla tej grupy rejestracji, wyłączyć lub usunąć poszczególne urządzenia z rejestru tożsamości, jego odpowiedniej usługi IoT Hub. 
  3. Po wyłączeniu lub usunięcie wszystkich urządzeń z ich odpowiednimi centra IoT Hub, możesz opcjonalnie usunąć grupę rejestracji. Należy pamiętać, jednak, aby usunąć grupę rejestracji, jeśli ma grupę rejestracji włączone dla certyfikatu podpisywania wyżej w łańcuchu certyfikatów co najmniej jedno z urządzeń, te urządzenia mogą ponownie zarejestrować. 

- Aby anulować aprowizację pojedynczego urządzenia z grupy rejestracji:
  1. Utwórz wyłączone rejestrację indywidualną dla swojego certyfikatu liścia (urządzenia). Nadal, umożliwiając dostęp do innych urządzeń, które mają grupy rejestracji certyfikatu podpisywania w łańcuchu ich spowoduje odwołanie dostępu do usługi aprowizacji dla tego urządzenia. Nie usuwaj wyłączone rejestrację indywidualną dla urządzenia. Takie postępowanie umożliwi urządzenia ponownie zarejestrować się za pośrednictwem grupy rejestracji. 
  2. Użyj listy aprowizowanych urządzeń dla tej grupy rejestracji można znaleźć w Centrum IoT, które urządzenie zostało zaaprowizowane do i Wyłącz lub usuń go z rejestru tożsamości w tym Centrum. 
  
  










