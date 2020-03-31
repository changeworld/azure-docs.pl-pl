---
title: Urządzenia deprovision, które zostały zainicjowane za pomocą usługi inicjowania obsługi administracyjnej usługi Azure IoT Hub Device Provisioning
description: Jak wyrejestrować urządzenia, które zostały aprowidzone za pomocą usługi inicjowania obsługi administracyjnej usługi azure ioT Hub (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 05/11/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 8a3677ba285f5b02407ca3d176979bf6c016ef9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974840"
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>Jak wyrejestrować urządzenia, które były wcześniej automatycznie aprowizacji 

Może się okazać, że konieczne może być usunięcie urządzeń aprowizujących, które wcześniej były automatycznie aprowizacji za pośrednictwem usługi aprowizacji urządzeń. Na przykład urządzenie może zostać sprzedane lub przeniesione do innego centrum IoT hub lub może zostać utracone, skradzione lub w inny sposób naruszone. 

Ogólnie rzecz biorąc, anulowanie obsługi administracyjnej urządzenia obejmuje dwa kroki:

1. Odłącz urządzenie od usługi inicjowania obsługi administracyjnej, aby zapobiec przyszłej automatycznej inicjowaniu obsługi administracyjnej. W zależności od tego, czy chcesz odwołać dostęp tymczasowo czy na stałe, można wyłączyć lub usunąć wpis rejestracji. W przypadku urządzeń korzystających z zaświadczania X.509 można wyłączyć/usunąć wpis w hierarchii istniejących grup rejestracji.  
 
   - Aby dowiedzieć się, jak wyrejestrować urządzenie, zobacz [Jak odłączyć urządzenie z usługi inicjowania obsługi administracyjnej urządzeń usługi Usługi Azure IoT Hub.](how-to-revoke-device-access-portal.md)
   - Aby dowiedzieć się, jak programowo wyrównywać urządzenie przy użyciu jednego z sdk usługi inicjowania obsługi administracyjnej, zobacz [Zarządzanie rejestracjami urządzeń za pomocą sdk usług](how-to-manage-enrollments-sdks.md).

2. Wyrejestruj urządzenie z centrum IoT Hub, aby zapobiec przyszłej komunikacji i transferowi danych. Ponownie można tymczasowo wyłączyć lub trwale usunąć wpis urządzenia w rejestrze tożsamości dla Usługi IoT Hub, gdzie został zainicjowany. Zobacz [Wyłączanie urządzeń,](/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) aby dowiedzieć się więcej o wyłączeniu. Zobacz "Zarządzanie urządzeniami / urządzeniami IoT" dla zasobu Usługi IoT Hub w [witrynie Azure portal](https://portal.azure.com).

Dokładne kroki, które można wykonać w celu anulowania obsługi administracyjnej urządzenia, zależą od mechanizmu zaświadczania i odpowiedniego wpisu rejestracji w usłudze inicjowania obsługi administracyjnej. Poniższe sekcje zawierają przegląd procesu, na podstawie typu rejestracji i zaświadczania.

## <a name="individual-enrollments"></a>Rejestracje indywidualne
Urządzenia korzystające z zaświadczania modułu TPM lub zaświadczania X.509 z certyfikatem typu liść są aprowidyfikowane za pośrednictwem indywidualnego wpisu rejestracji. 

Aby anulować aprowizję urządzenia z indywidualną rejestracją: 

1. Odłącz urządzenie od usługi aprowizacji:

   - W przypadku urządzeń korzystających z zaświadczania modułu TPM usuń indywidualny wpis rejestracji, aby trwale odwołać dostęp urządzenia do usługi inicjowania obsługi administracyjnej lub wyłączyć wpis, aby tymczasowo odwołać jego dostęp. 
   - W przypadku urządzeń korzystających z zaświadczenia X.509 można usunąć lub wyłączyć wpis. Należy jednak pamiętać, jeśli usuniesz indywidualną rejestrację dla urządzenia, które używa X.509 i istnieje włączona grupa rejestracji dla certyfikatu podpisywania w łańcuchu certyfikatów tego urządzenia, urządzenie może ponownie zarejestrować. W przypadku takich urządzeń może być bezpieczniejsze wyłączenie wpisu rejestracji. Zapobiega to ponownemu rejestrowaniu urządzenia, niezależnie od tego, czy dla jednego z jego certyfikatów podpisywania istnieje włączona grupa rejestracji.

2. Wyłącz lub usuń urządzenie w rejestrze tożsamości centrum IoT hub, który został zainicjowany. 


## <a name="enrollment-groups"></a>Grupy rejestracji
Z X.509 zaświadczania, urządzenia mogą być również aprowizacji za pośrednictwem grupy rejestracji. Grupy rejestracji są konfigurowane z certyfikatem podpisywania, certyfikatem pośredniego lub głównego urzędu certyfikacji i kontrolują dostęp do usługi inicjowania obsługi administracyjnej dla urządzeń z tym certyfikatem w łańcuchu certyfikatów. Aby dowiedzieć się więcej o grupach rejestracji i certyfikatach X.509 w usłudze inicjowania obsługi administracyjnej, zobacz [Certyfikaty X.509](concepts-security.md#x509-certificates). 

Aby wyświetlić listę urządzeń, które zostały zainicjowane za pośrednictwem grupy rejestracji, można wyświetlić szczegóły grupy rejestracji. Jest to łatwy sposób, aby zrozumieć, które Centrum IoT hub zostało aprowione do każdego urządzenia. Aby wyświetlić listę urządzeń: 

1. Zaloguj się do witryny Azure portal i kliknij pozycję **Wszystkie zasoby** w menu po lewej stronie.
2. Kliknij usługę inicjowania obsługi administracyjnej na liście zasobów.
3. W usłudze inicjowania obsługi administracyjnej kliknij pozycję **Zarządzaj rejestracjami,** a następnie wybierz pozycję **Grupy rejestracji.**
4. Kliknij grupę rejestracji, aby ją otworzyć.

   ![Wyświetlanie wpisu grupy rejestracji w portalu](./media/how-to-unprovision-devices/view-enrollment-group.png)

W przypadku grup rejestracji istnieją dwa scenariusze, które należy wziąć pod uwagę:

- Aby anulować anulowanie obsługi administracyjnej wszystkich urządzeń, które zostały zainicjowane za pośrednictwem grupy rejestracji:
  1. Wyłącz grupę rejestracji, aby na czarnej liście jej certyfikat podpisywania. 
  2. Użyj listy aprowizowanych urządzeń dla tej grupy rejestracji, aby wyłączyć lub usunąć każde urządzenie z rejestru tożsamości odpowiedniego centrum IoT. 
  3. Po wyłączeniu lub usunięciu wszystkich urządzeń z odpowiednich centrów IoT można opcjonalnie usunąć grupę rejestracji. Należy jednak pamiętać, że jeśli usuniesz grupę rejestracji i istnieje włączona grupa rejestracji dla certyfikatu podpisywania wyżej w łańcuchu certyfikatów jednego lub więcej urządzeń, te urządzenia mogą ponownie się zarejestrować. 

- Aby anulować aprovision pojedynczego urządzenia z grupy rejestracji:
  1. Utwórz wyłączona rejestracja indywidualna dla swojego certyfikatu liścia (urządzenia). Spowoduje to odwołanie dostępu do usługi inicjowania obsługi administracyjnej dla tego urządzenia, jednocześnie zezwalając na dostęp dla innych urządzeń, które mają certyfikat podpisywania grupy rejestracji w łańcuchu. Nie należy usuwać wyłączonej rejestracji indywidualnej dla urządzenia. Umożliwi to urządzeniu ponowne zarejestrowanie się za pośrednictwem grupy rejestracji. 
  2. Użyj listy aprowizowanych urządzeń dla tej grupy rejestracji, aby znaleźć centrum IoT hub, którego urządzenie zostało zainicjowane, oraz wyłączyć lub usunąć je z rejestru tożsamości tego centrum. 
  
  










