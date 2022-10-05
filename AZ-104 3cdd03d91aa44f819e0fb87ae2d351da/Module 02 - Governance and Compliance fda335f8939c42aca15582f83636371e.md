# Module 02 - Governance and Compliance

## 🌎 Regiones

- Colección de datacenters.
- Probé flexibilidad y escalado.
- Conserva los datos de forma “residencial”.
- Es recomendable utilizar regiones cerca del usuario final.
- Existen servicios globales que son independientes a las regiones.
- Están pareadas para tener una **alta disponibilidad**.

## 📜 Azure Subscriptions

- Unidad lógica del servicio de Azure que está enlazada a una cuenta de Azure.
- Están aisladas unas con otras a nivel de seguridad y de facturación.
- Incluye cuentas: identidades de Azure AD o en directorio de confianza (trabajo o colegio)

**Utilización de la suscripción**

| Subscription | Usage |
| --- | --- |
| Free | 200$ de crédito durante 30 días. Acceso gratuito y limitado durante 12 meses |
| Pay-As-You-Go | Pago mensual |
| Enterprise | Acuerdo que incluye descuentos |
| Student | 100$ durante 12 meses |

![Untitled](Module%2002%20-%20Governance%20and%20Compliance%20fda335f8939c42aca15582f83636371e/Untitled.png)

## Conceptos

- **Identidad / Identity:** objeto que puede autenticarse.
- **Cuenta / Account:** identidad que tiene datos asociados.
- **Azure AD Account:** identidad creada a través de Azure AD u otro servicio cloud de MS.
- **Azure Tenant:** instancia segura y dedicada de Azure AD que se crea automáticamente cuando la organización se registra en una suscripción Cloud. Cada tenant es independiente el uno del otro (recursos, administración y sincronización) y no hay relación padre-hijo entre ellos.
- **Azure AD Directory:** cada tenant de Azure tiene un directorio de Azure AD dedicado y confiable.
- **Suscripción / User suscription:** utilizado para pagar por los servicios de Azure cloud.

## Azure AD vs Active Directory

1. Azure AD es una solución de identidad diseñada sobre comunicaciones HTTP/HTTPS.
2. La consultas se realizan mediante REST API (HTTP/HTTPS) en vez de LDAP.
3. Para autenticación utiliza procolos como SAML, WS-Federation y OpenID Connect en vez Kerberos.
4. Incluye servicios de federación y servicios *third-party*.
5. Los usuarios de Azure AD se crean en una estructura plana y no existen OUs o GPOs.
6. Se pueden gestionar tanto servicios cloud como Onpremise con las identidades creadas en Azure AD.

## Multi-Factor Authentication & Self-service password reset

- Proporciona 2FA (doble factor de autenticación).
- Permite forzar controles de acceso en función de condiciones específicas.

![Condición: “cuando esto suceda” / Control de acceso: “realiza esto otro”](Module%2001%20-%20Identity%20df73088e16964a968e01f61237411665/Untitled%201.png)

Condición: “cuando esto suceda” / Control de acceso: “realiza esto otro”

- Se puede especificar quién puede utilizar el *serf-service password reset*.
- Se puede escoger el nº de métodos de autenticación que se solicitan al hacer login: email, teléfono, pregunta…

## User Accounts

- Las cuentas se utilizan para **autenticar** y **autorizar**.
- Tipos: cloud, sincronizadas con el directorio e invitados.
- Hay que ser **Global Administrator** para **gestionar** cuentas de usuario.
- **Usuarios eliminados** se pueden **recuperar** durante **30 días**.
- Se pueden dar de alta de manera masiva (*bulk*). [Ampliar información](https://learn.microsoft.com/en-us/azure/active-directory/enterprise-users/users-bulk-add)

## Group Accounts

**Group types**

- Security Groups
- Office 365 Groups

**Assignment Types**

- Assigned
- Dynamic User
- Dynamic Device (Security Groups only)

![Untitled](Module%2001%20-%20Identity%20df73088e16964a968e01f61237411665/Untitled%202.png)

<aside>
👥 **Roles Azure AD**: [más información](https://learn.microsoft.com/en-us/azure/active-directory/roles/permissions-reference)

</aside>

## Cloud Identity

### **Cloud-only identity**

- Cuentas de usuario existen únicamente en Azure AD.
- Utilizado por pequeñas y medianas empresas que no tiene un AD DS.

### **Hybrid identity**

- Utiliza cuentas que, originalmente, estaban en un AD DS on-premise y que se copian en un Azure AD.
- Los cambios que se realizan en el AD DS se sincronizan en Azure AD, pero **no al revés**. El AD DS es autoritativo por lo que las tareas de administración se realizan en este.
- Para sincronizar la contraseña realiza un *hash* de la contraseña ya *hasheada (password hash synchronization **PHS**)*
- Es necesario utilizar **Azure AD Connect** para realizar dicha sincronización.

### **Autenticación**

- **Managed authentication** (autenticación administrada)
    - 📄 Azure AD gestiona el proceso de autenticación utilizando un *hash* almacenado de la contraseña ya *hasheada* o envía la credencial a un sw de un agente on-premise para que autentique con el AD DS on-premise.
    - ▶️ **Password hash synchronization (PHS)**: Azure AD realiza la autenticación él mismo.
        - Sincroniza la cuenta del usuario desde el AD DS a Azure AD.
        - Los usuarios se gestionan *on-premise.*
        - Se sincronizan los *hashes* de las contraseñas ya *hasheadas* en AD DS por lo que el usuario tendrá la misma contraseña que en *on-premise*.
        - Si la contraseña se cambia o resetea, el nuevo *hash* se sincronizará en el Azure AD.
        
        ![https://learn.microsoft.com/en-us/microsoft-365/media/plan-for-directory-synchronization/phs-authentication.png?view=o365-worldwide](https://learn.microsoft.com/en-us/microsoft-365/media/plan-for-directory-synchronization/phs-authentication.png?view=o365-worldwide)
        
        <aside>
        👉 Métodos de autenticación: [más información](https://learn.microsoft.com/en-us/azure/active-directory/hybrid/choose-ad-authn)
        
        </aside>
        
    - ▶️ **Pass-through authentication (PTA)**: Azure AD hace que AD DS realice la autenticación.
        - La validación de la contraseña se realiza utilizando un agente sw que corre en uno (o más) servidores *on-premise* para validar el usuario directamente en el AD DS.
        - Los usuarios se gestionan de manera *on-premise*.
        - PTA permite al usuario conectarse tanto en recursos de Microsoft 365 como *on-premise*.
        - Valida la contraseña directamente en el AD DS sin almacenar el *hash* en Azure AD.
        - Está enfocado para organizaciones con unos requisitos de seguridad más elevados y que controlen los estados de las cuentas de usuarios: hora de conexión, políticas de contraseña, etc.
        
        ![https://learn.microsoft.com/en-us/microsoft-365/media/plan-for-directory-synchronization/pta-authentication.png?view=o365-worldwide](https://learn.microsoft.com/en-us/microsoft-365/media/plan-for-directory-synchronization/pta-authentication.png?view=o365-worldwide)
        
    - ▶️ **Federated authentication** (autenticación federada)
        - Azure AD redirige la petición de autenticación del cliente a otro proveedor de identidad.
        - Orientada a grandes empresas con unos requisitos de autenticación complejos.
        - Las identidades de AD DS están sincronizadas con MS365 y las cuentas de usuario están gestionadas de manera *on-premise*.
        - Los usuarios tienen la misma contraseña en cloud y en *on-premise* y no tienen que logarse de nuevo para utilizar MS365.
        - También soporta: smartcard o un *3th party multi-factor authenticator*.
    
    <aside>
    👉 **Azure AD federation compatibility**: [más información](https://learn.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-fed-compatibility)
    
    </aside>
    

| Attribute | Cloud-only identity | Hybrid identity |
| --- | --- | --- |
| Definition | User account only exists in the Azure AD tenant for your Microsoft 365 subscription. | User account exists in AD DS and a copy is also in the Azure AD tenant. The user account in Azure AD might also include a hashed version of the already hashed AD DS user account password. |
| How Microsoft 365 authenticates user credentials | Authenticate with the cloud identity account. | The Azure AD tenant either handles the authentication process or redirects the user to another identity provider. |
| Best for | Organizations that do not have or need an on-premises AD DS. | Organizations using AD DS or another identity provider. |
| Greatest benefit | Simple to use. No extra directory tools or servers required. | Users can use the same credentials when accessing on-premises or cloud-based resources. |

<aside>
👉 **Identidad:** [ampliar información](https://learn.microsoft.com/en-us/microsoft-365/enterprise/deploy-identity-solution-identity-model)

</aside>

## Azure AD Connect

- Permite integrar el AD *on-premise* con Azure Active Directy
- Prové una identidad común para los usuarios de Office 365, Azure y aplicaciones SaaS

<aside>
👉 **Azure AD Connect:** [Ampliar información](https://learn.microsoft.com/es-es/azure/active-directory/hybrid/whatis-azure-ad-connect)

</aside>

## Más información

> ****[Protect your Microsoft 365 privileged accounts](https://learn.microsoft.com/en-us/microsoft-365/enterprise/protect-your-global-administrator-accounts?view=o365-worldwide)****
****[Protect your Microsoft 365 user accounts](https://learn.microsoft.com/en-us/microsoft-365/enterprise/microsoft-365-secure-sign-in?view=o365-worldwide)
Deploy your identity model: [cloud-only identity](https://learn.microsoft.com/en-us/microsoft-365/enterprise/cloud-only-identities?view=o365-worldwide) o [hybrid identity](https://learn.microsoft.com/en-us/microsoft-365/enterprise/prepare-for-directory-synchronization?view=o365-worldwide)**
> 

- Permite integ

## 🏗️ Lab 01 - Manage Azure Active Directory Identities

### Lab scenario

In order to allow Contoso users to authenticate by using Azure AD, you have been tasked with provisioning users and group accounts. Membership of the groups
should be updated automatically based on the user job titles. You also need to create a test Azure AD tenant with a test user account and grant that account
limited permissions to resources in the Contoso Azure subscription.

### Objectives

- Task 1: Create and configure Azure AD users
- Task 2: Create Azure AD groups with assigned and dynamic membership
- Task 3: Create an Azure Active Directory (AD) tenant
- Task 4: Manage Azure AD guest users