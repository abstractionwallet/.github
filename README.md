# .github
alchemy account abstraction quick links reference<br />
https://accountkit.alchemy.com/<br />
https://demo.alchemy.com/<br />
https://www.alchemy.com/blog/introducing-account-kit<br />
https://www.alchemy.com/smart-wallets<br />
https://accountkit.alchemy.com/react/quickstart<br />

Integrating Alchemy Account Kit v4.x.x with Next.js for Account Abstraction
Account Abstraction (AA) is emerging as a pivotal advancement in the Web3 landscape, addressing the complexities often associated with traditional cryptocurrency wallets and aiming to provide a more seamless user experience. By enabling smart contracts to function as user-controlled accounts, AA introduces possibilities such as social recovery, multi-signature authorization, and the abstraction of gas fees, aligning the user experience more closely with the familiar interactions of Web2 applications. Alchemy Account Kit serves as a comprehensive toolkit designed to simplify the integration of these powerful AA features into Web3 applications. This kit provides developers with a suite of tools, including UI components, React Hooks, and backend infrastructure, to facilitate the creation of AA-enabled applications without delving into the intricate details of ERC-4337.
The combination of Alchemy Account Kit's React-based SDK and the Next.js framework presents a robust solution for building scalable and performant full-stack Web3 applications. Next.js, with its features like server-side rendering (SSR) and efficient routing, complements the functionalities of the Account Kit, leading to enhanced application performance and improved search engine optimization. This report aims to provide a detailed, step-by-step guide for developers seeking to integrate Alchemy Account Kit v4.x.x into their Next.js projects using npm, establishing a solid foundation for leveraging the benefits of account abstraction.
For developers embarking on a new Next.js project, Alchemy offers a convenient template that streamlines the initial setup process. By executing the command yarn create next-app account-kit-app -e https://github.com/avarobinson/account-kit-quickstart or its npm equivalent, a new project is bootstrapped with Account Kit integration already configured 5. This template provides a functional example out of the box, significantly reducing the time and effort required for initial setup and offering a working reference point for further development.
For those integrating Alchemy Account Kit into an existing Next.js project, a series of straightforward steps ensures a smooth process. The integration requires a minimum React version of 18 and TypeScript version of 5 5. It is also recommended to pin the versions of core dependencies to ensure compatibility: viem@2.20.0 and wagmi@2.12.7 5. These specific versions are known to work well with the Account Kit SDK, preventing potential issues arising from updates in newer versions. The core Account Kit packages, along with a data fetching library, are installed using the command: yarn add @account-kit/infra @account-kit/react @tanstack/react-query. These packages provide the foundational infrastructure, React-specific components, and asynchronous data management capabilities necessary for the integration. Styling for the Account Kit UI components relies on Tailwind CSS, which, along with its peer dependencies, is installed using: yarn add -D tailwindcss postcss autoprefixer 5. Tailwind CSS is a prerequisite for utilizing the pre-built UI components offered by the Account Kit, while PostCSS and Autoprefixer are essential for processing and ensuring cross-browser compatibility of the Tailwind styles.
To configure Tailwind CSS for styling the Account Kit UI components, developers can initiate the setup by running npx tailwindcss init -p if these files are not already present in the project 5. This command generates tailwind.config.ts and postcss.config.ts files. The postcss.config.ts file needs to be configured to include the Tailwind CSS and Autoprefixer plugins to ensure proper processing of styles:

```JavaScript
// postcss.config.mjs
export default {
  plugins: {
    "@tailwindcss/postcss": {},
    "autoprefixer": {},
  },
};
```

This configuration allows PostCSS to use the Tailwind CSS plugin for transforming styles and Autoprefixer to add necessary vendor prefixes for broader browser support. Finally, a global.css file should be created or updated to include the Tailwind CSS directives, which inject the base, component, and utility styles of Tailwind into the application 5:
```css
/* global.css */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

The Alchemy Account Kit v4.x.x SDK is structured into several modular packages, all prefixed with @account-kit/, allowing developers to select the level of abstraction that suits their project's needs. Key packages include @account-kit/react, @account-kit/core, @account-kit/infra, @account-kit/signer, and @account-kit/smart-contracts. The @account-kit/react package provides React-specific components and hooks, simplifying the integration of authentication and UI elements. The @account-kit/core package offers core functionalities and abstractions for interacting with the Account Kit infrastructure, independent of any UI framework. It serves as the central hub for authentication logic and smart account management. For developers requiring more granular control, the @account-kit/infra package provides lower-level tools for direct interaction with Alchemy's AA infrastructure, enabling custom implementations of signers and smart contracts. This package is particularly useful for advanced use cases involving custom smart contract logic or specific gas management requirements. The modularity of this structure allows for code reusability and helps in reducing the application's bundle size by importing only the necessary modules.
For implementing user authentication, Alchemy Account Kit provides pre-built UI components that streamline the process. Modal-based authentication can be easily implemented using the useAuthModal hook from @account-kit/react. By importing this hook and calling the openAuthModal function within a button's onClick handler, a clean and focused authentication modal is presented to the user. This approach offers a non-disruptive way for users to initiate the login process 10. Alternatively, embedded authentication can be integrated using the AuthCard component, also available from @account-kit/react. Rendering the AuthCard component directly within the application's layout or on a dedicated login page provides a more integrated authentication experience.
The look and feel of the authentication UI can be extensively customized using the AlchemyAccountsUIConfig object. By creating a config.ts file, developers can define the UI configuration using the auth property within this object. The sections array within the auth property allows for the ordering and grouping of different authentication options, each defined as an AuthTypes object. Available AuthTypes include "email" for email-based login (OTP and Magic Link), "passkey" for passkey login and signup, "external_wallets" for connecting existing Externally Owned Accounts (EOAs) via browser extensions or WalletConnect, and "social" for integrating social login providers like Google and Facebook. Configuring these types allows developers to tailor the authentication interface to match their application's branding and desired user flow.
For developers who require complete control over the authentication UI and flow, Alchemy Account Kit offers React Hooks. The useAuthenticate hook, imported from @account-kit/react, is fundamental for initiating the authentication process for various methods like email, social login, and passkeys. The authenticate function returned by this hook is used with specific parameters depending on the chosen method. For social login, the hook supports both redirect and popup flows, with the latter requiring the enablePopupOauth option to be set to true in the configuration. The isPending property returned by the hook helps in managing loading states during authentication. The useUser hook provides access to the authenticated user's information, such as their address and account type ("eoa" or "sca"), simplifying the process of checking login status and accessing user details. To interact with the user's smart contract account, the useAccount hook can be used. This hook returns the smart contract account instance (account), its address, and a loading state (isLoadingAccount), enabling actions like sending transactions on behalf of the user's smart account.
Alchemy Account Kit supports a variety of login providers, each configurable to suit different application requirements. Email OTP and Email Magic Link are enabled by toggling the "Email auth" option in the Account Kit Dashboard and specifying a Redirect URL. Social login providers like Google and Facebook can be enabled by toggling the respective options in the dashboard, adding http://localhost:3000 as a whitelisted origin for testing, and the application's production URL for deployment. Passkey authentication, with email backup and existing passkey login, is also configurable within the dashboard. The authenticate function is used with the appropriate type (e.g., "email", "oauth", "passkey") to initiate the login flow for each provider. All these configurations are managed within the Alchemy Account Kit Dashboard, where a new account config is created and applied to the specific Alchemy app, allowing for customization of authentication methods.
The AlchemyAccountProvider component, imported from @account-kit/react, plays a crucial role in integrating Alchemy Account Kit into a React application. It acts as a React Context provider, wrapping the application to provide access to the Account Kit's functionalities and manage the authentication state 5. This provider initializes the Alchemy client and makes it accessible to its descendant components through React Context, handling the underlying logic for authentication and smart account management. It requires several key props for proper configuration:





AlchemyClientState (from @account-kit/core)
Optional. Used specifically for Server-Side Rendering (SSR) in Next.js applications. This prop allows you to initialize the Account Kit state on the server with data typically retrieved from cookies, ensuring state persistence across page loads.

In a Next.js application, the AlchemyAccountProvider is typically used within the app/providers.tsx file, wrapping the application with both QueryClientProvider and AlchemyAccountProvider. The queryClient and config objects are passed to their respective providers. For server-side rendering, the initialState prop is utilized in the app/layout.tsx file. By importing cookieToInitialState from @account-kit/core and headers from next/headers, the initial state is retrieved from cookies and passed to the AlchemyAccountProvider, ensuring state persistence across page loads.
A successful integration of Alchemy Account Kit requires several npm dependencies. @account-kit/infra provides the necessary infrastructure layer. @account-kit/react contains the React-specific components and hooks. @tanstack/react-query is used for asynchronous state management. tailwindcss is essential for styling the UI components. postcss and autoprefixer are required for processing Tailwind CSS. Additionally, viem and wagmi are used internally by the Account Kit for blockchain interactions. These dependencies are typically installed using npm install or yarn add. It is crucial to pin the versions of viem (e.g., 2.20.0) and wagmi (e.g., 2.12.7) as recommended in the documentation to ensure stability and compatibility.
The Alchemy demo available at https://demo.alchemy.com/ offers valuable insights into the practical implementation of the Account Kit. It showcases various authentication methods, including email, social logins, and passkeys, and demonstrates the use of pre-built UI components for login and wallet connection. The demo also highlights customization options for branding and appearance and illustrates the functionality of "Smart Accounts," including "Smart EOA (EIP-7702)". By exploring this demo, developers can observe how different authentication methods are configured within the AlchemyAccountsUIConfig and how the useAuthModal hook is used to initiate the authentication flow. The demo likely provides examples of using other Account Kit hooks for accessing user and account information, serving as a practical guide for integration strategies.
For Next.js integration, implementing Server-Side Rendering (SSR) is crucial for maintaining a consistent account state between the server and the client. This is achieved by setting the ssr option to true in the createConfig function and using cookieStorage for persisting account state across page loads. In the layout.tsx file, the initialState prop of AlchemyAccountProvider is used in conjunction with cookieToInitialState to hydrate the client-side state from server-rendered cookies. This approach not only enhances the user experience through faster initial page loads but also ensures consistent authentication state throughout the application. Security is paramount when handling API keys and authentication credentials. It is recommended to protect the Alchemy API key, especially in production, by potentially proxying requests through a backend. Best practices for managing OAuth credentials should be followed if custom credentials are used. Additionally, ensuring that redirect URLs are correctly configured in the Account Kit Dashboard helps prevent unauthorized access. While Alchemy Account Kit provides extensive functionality, it also offers flexibility for customization and extension. Developers can leverage React Hooks to build entirely custom authentication UIs and flows or explore the lower-level packages for advanced use cases. The AlchemyAccountsUIConfig also provides various customization options for theming and branding.
Integrating Alchemy Account Kit v4.x.x into a Next.js project using npm provides a robust foundation for leveraging the benefits of account abstraction. By following the outlined steps for project setup, dependency management, UI component utilization, React Hook implementation, login provider configuration, and understanding the role of the AlchemyAccountProvider, developers can confidently embark on building user-friendly and powerful Web3 applications. The insights gained from the Alchemy demo and the consideration of advanced topics like SSR and security further solidify the integration process. This guide serves as a comprehensive point of departure, equipping developers with the knowledge and resources necessary to successfully integrate Alchemy Account Kit and enhance the user experience of their Next.js Web3 applications.
Works cited
```txt
Alchemy Account Kit
https://accountkit.alchemy.com/
Alchemy Account Kit - Lit Protocol
https://developer.litprotocol.com/integrations/aa/alchemy-account-kit
Alchemy Account Abstraction (AA) SDK for Golang - GitHub
https://github.com/anyproto/alchemy-aa-sdk
alchemyplatform/aa-sdk - Account Kit - GitHub
https://github.com/alchemyplatform/aa-sdk
React Quickstart – Account Kit
https://accountkit.alchemy.com/react/quickstart
avarobinson/account-kit-quickstart: NextJS template for embedded accounts UI components alpha quickstart guide - GitHub
https://github.com/avarobinson/account-kit-quickstart
Rendering: Client Components - Next.js
https://nextjs.org/docs/app/building-your-application/rendering/client-components
Core Quickstart - Alchemy Account Kit
https://accountkit.alchemy.com/core/quickstart
Quickstart - Alchemy Account Kit
https://accountkit.alchemy.com/infra/quickstart
Authentication with UI componenets – Account Kit
https://accountkit.alchemy.com/react/ui-components
Getting started with authentication - Alchemy Account Kit
https://accountkit.alchemy.com/react/getting-started
Authentication with our React hooks - Alchemy Account Kit
https://accountkit.alchemy.com/react/react-hooks
Authenticate Users - Alchemy Account Kit
https://accountkit.alchemy.com/core/authenticate-users
Account Kit Demo https://demo.alchemy.com/
```
