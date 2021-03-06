# CAS (Central Authentication Service) Single Sign On Authentication [](id=cas-central-authentication-service-single-sign-on-authentication)

CAS is an authentication system originally created at Yale University. It is a
widely used open source single sign-on solution and was the first SSO product
to be supported by Liferay. Please follow the documentation for CAS to install
it on your application server of choice.

Your first step is to copy the CAS client `.jar` file to Liferay's library
folder. On Tomcat, this is in `[Tomcat Home]/webapps/ROOT/WEB-INF/lib`. Once
you've done this, the CAS client will be available to Liferay the next time you
start it.

The CAS Server application requires your server to have a properly configured
Secure Socket Layer (SSL) certificate. If you wish to generate one yourself,
you will need to use the `keytool` utility that comes with the JDK. Your first
step is to generate the key. Next, you export the key into a file. Finally, you
import the key into your local Java key store. For public, internet-based
production environments, you will need to either purchase a signed key from a
recognized certificate authority (such as Thawte or Verisign) or have your key
signed by a recognized certificate authority. For intranets, you should have
your IT department pre-configure users' browsers to accept the certificate so
they don't get warning messages about the certificate.

To generate a key, use the following command:

    keytool -genkey -alias tomcat -keypass changeit -keyalg RSA

Instead of the password in the example (`changeit`), use a password you will
remember. If you are not using Tomcat, you may want to use a different alias as
well. For first and last names, enter `localhost` or the host name of your
server. It cannot be an IP address.

To export the key to a file, use the following command:

    keytool -export -alias tomcat -keypass changeit -file server.cert

Finally, to import the key into your Java key store, use the following command:

    keytool -import -alias tomcat -file %FILE_NAME% -keypass changeit -keystore $JAVA_HOME/jre/lib/security/cacerts

If you are on a Windows system, replace `$JAVA_HOME` above with `%JAVA_HOME%`.
Of course, all of this needs to be done on the system on which CAS will be
running.

Once your CAS server is up and running, you can configure Liferay to use it.
CAS configuration can be applied either at the system scope or at the scope of
a portal instance. To configure the CAS SSO module at the system scope,
navigate to the Control Panel, click on *System* &rarr; *System Settings, click
on the *Platform* category, and find the CAS module. The values configured
there provide the default values for all portal instances. Enable CAS
authentication and then modify the URL properties to point to your CAS server.

**Enabled:** Check this box to enable CAS single sign-on.

**Import from LDAP:** A user may be authenticated from CAS and not yet exist in
the portal. Select this to automatically import users from LDAP if they do not
exist in the portal. (For this to work, LDAP must be enabled.)

The rest of the settings are various URLs, with defaults included. Change
*localhost* in the default values to point to your CAS server. When you are
finished, click *Save*. After this, when users click the *Sign In* link, they
will be directed to the CAS server to sign in to Liferay.

For some situations, it might be more convenient to specify the system
configuration via files on the disk. To do so, simply create the following
file:

    {LIFERAY_HOME}/osgi/configs/com.liferay.portal.security.sso.cas.module.configuration.CASConfiguration.cfg

The format of this file is the same as any properties file. The key to use for
each property that can be configured is shown below. Enter values in the same
format as you would when initializing a Java primitive type with a literal
value.

Property Label | Property Key | Description | Type
:----: | :----: | :----: | :----:
**Enabled** | `enabled` | Check this box to enable CAS SSO authentication. | `boolean`
**Import from LDAP** | `importFromLDAP` | Users authenticated from CAS that do not exist in Liferay are imported from LDAP. LDAP must be enabled separately. | `boolean`
**Login URL** | `loginURL` | Set the CAS server login URL. | `String`
**Logout on session expiration** | `logoutOnSessionExpiration` | If checked, browsers with expired sessions are redirected to the CAS logout URL. | `boolean`
**Logout URL** | `logoutURL` | The CAS server logout URL. Set this if you want Liferay's logout function to trigger a CAS logout | `String`
**Server Name** | `serverName` | The name of the Liferay portal (e.g., `liferay.com`). If the provided name includes the protocol (`https://`, for example) then this will be used together with the path `/c/portal/login` to construct the URL to which the CAS server will provide tickets. If no scheme is provided, the scheme normally used to access the Liferay login page will be used. | `String`
**Server URL** | `serviceURL` | If provided, this will be used as the URL to which the CAS server provides tickets. This overrides any URL constructed based on the Server Name as above. | `String`
**No Such User Redirect URL** | `noSuchUserRedirectURL` | Set the URL to which to redirect the user if the user can authenticate with CAS but cannot be found in Liferay. If import from LDAP is enabled, the user is redirected if the user could not be found or could not be imported from LDAP. | `String`

To override system defaults for a particular portal instance, navigate to the
Control Panel, click on *Configuration* &rarr; *Instance Settings*, click on
*Authentication* on the right and then on *CAS* at the top.

## Summary [](id=summary)

System administrators sometimes think it's sufficient to link multiple webapps
to a single user directory such as LDAP. This certainly goes a long way towards
providing a convenient user experience. However, if this approach is taken, the
overall security of your users' credentials is reduced to that of the weakest
webapp. Thus, this approach should only be taken if you're extremely confident
in the security of each linked webapp. If you're not, you should consider an
alternative approach to authentication.

CAS authentication is very straight-forward to set up. Using it removes the
risk associated with asking users to enter their credentials into multiple web
applications.
