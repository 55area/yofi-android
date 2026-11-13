
--------------------------------------------------------------------------------

I'm an Android Engineering Manager working in the payment terminal space, the terminal market is moving swiftly to Android (has been for some time) there are many payment related activities you can build apps for. There is no central store like the Play Store each hardware vendor has a unique way of distributing apps via stores or just the TMS. The easiest way to get into this space would be to research payments and start dabbling with ideas surrounding payments (working at the PCI L3 level - payment apps - will require corporate sponsorship but try to dig into it for understanding). PCI devices are similar to normal Android devices but no GMS certification. Research company's operating in this space and try to get a job starting at the bottom. We use AI but with far more human oversight. Best of both worlds. Notice all the different payment terminals and brands when out shopping. These are all targets for your applications. Good Luck.

--------------------------------------------------------------------------------

Se você estiver começando um Android app novo hoje com Kotlin, eu consideraria:

Kotlin
   │
   ├── Jetpack Compose       ← UI
   ├── Material 3            ← design
   ├── ViewModel             ← estado/lógica da UI
   ├── Navigation            ← navegação
   ├── Room                  ← banco local
   └── outras bibliotecas

* Um detalhe importante: Jetpack não é sinônimo de Compose. Jetpack é o conjunto maior de bibliotecas Android; Compose é o toolkit de UI dentro desse ecossistema.

--------------------------------------------------------------------------------

	100% Kotlin, 100% Jetpack Compose with Material 3 Expressive (it is still in alpha, but I tried to incorporate a lot from what I learned).

	Support for dynamic theme, phones, tablets and foldables (inspired by Grok app).

	New visual charts to see the distribution of SDK versions and recent updates.

	A custom-built fast scroller (inspired by Niagara Launcher) that lets you zip through your app list by letter or SDK version.

	Translated (by AI) into multiple languages: Portuguese, Italian, French, German, Japanese, Chinese and Spanish.

* To make it easier for the AI to understand the context, I grouped related files together (e.g., ViewModel + Screen + Components) in the same directory, a structure more common in web development. This meant I could just drag a single folder into the AI's context window.

* Since I already had a GitHub Copilot subscription, I would go to vscode, ask for changes, then go back to Android Studio and double check if syntax is correct, it compiles, etc. Ideally Copilot would have a feature complete plugin for Intellij based IDEs but it doesn't.

--------------------------------------------------------------------------------

It helps for spotting obvious issues or suggesting optimizations, but not great for real bottlenecks. Performance problems usually need profiling, not guesses.

Tools like Android Profiler still matter way more.

I’ve used Cursor for quick code review, and once ran a performance report through Runable just to structure findings better, but actual fixes still came from manual testing.


Yeah the tools are very good for almost everything. You just need to guide it properly. You can also just get them to create a bunch of logging with timers dotted around to find bottlenecks. But you can't really just tell it to find them unless you are able to narrow it down a bit for them. Unless the codebase is small.

--------------------------------------------------------------------------------

I'm an Android developer with 10 years of experience, and lately I've been doing almost all of my development with Claude Code.

I find this approach quite reasonable.

Generating an initial version of step 1 is entirely feasible. Just please pay close attention to security. The difficulty will vary depending on how sensitive the information being handled is. For an app that doesn't deal with personal or payment information, it's extremely easy, cheap, and fast.

Step 2 is naturally achievable, so there isn't much to add. If there's no demand, it would be wise to stop here and return to step 1.

If steps 1 and 2 indicate promising demand, then investing money to hire a "talented" engineer at this stage is, I believe, a truly rational decision. For something at the MVP level, even if you end up having to rebuild the whole thing from scratch, leveraging AI makes it entirely possible to do so quickly and in a sustainable manner.

The most difficult part is whether you can actually manage to hire a genuinely talented engineer at step 3.

--------------------------------------------------------------------------------

Use Pure Kotlin libraries for native app in Kotlin.

ANDROID TECH STACK:
	Storage:		Room Persistence Library

	Networking:		Ktor Network Library

	Presentation:	KMP, MVI, Flows, Coroutines
					Navigation 3
					Coil Image Loading Library

	DI:				Koin Dependency Injection Library

	Observability:	Kermit Multiplatform Logging Library
					Firebase Analytics

	Building:		Gradle

	CI/CD Pipeline:	Bitrise CI/CD for Native Apps

	Testing:		Assertk Assertions for Kotlin
					Turbine for Flow Testing
					Fakes (not Mocks) for Test Doubles / Unit Testing
					Compose UI Testing Framework

--------------------------------------------------------------------------------

Antigravity + Android Studio. 1st for agentic coding, 2nd for building, debugging, editing etc

Which mcp servers do you use ? 
Figma, Atlassian, Github(Junie doesn't need this, just does Git CLI) , ADB and Shell.

Antigravity for code, Android Studio to build.
	
--------------------------------------------------------------------------------

I have full workflow in Claude Code

Init ticket (gets info about ACs from the ticket) Ui analysis (checks Figma and based on that creates analysis and verifies if a component is in the codebase to reuse it) Implementation plan - creates plan how to create a code

And a few others, but I mostly use that.

Each step = new session and each step produces a new md file. I review and fix those md files (it is rare to have a one shot problem keeping high quality) and then let AI implement the rest. Then I review the code. It is called specification driven development.

If AI fails I either change Claude Code's plugin or Claude.md rules accordingly (if it makes sense), but random stupid things might appear anyway, not worth chasing every possible rule.

Effect: It writes about 90% of the code, but without technical reviews the code quality would be bad. So it still requires knowledge and effort. But saves a lot of time

--------------------------------------------------------------------------------

THE REAL CAUSES OF APP ABANDONMENT:

	1. Performance failure. Freezes, crashes and slow response are the most-cited reasons users walk. Google Play now treats a user-perceived crash rate at or above 1.09% as “bad behaviour” and will reduce your store visibility for it, so technical debt turns into a growth tax.

	2. Confusing or heavy UX. If users can’t understand the interface, or the signup wall lands before any value, they bail. Every extra onboarding screen compounds. Past two minutes, you’ve usually lost the cohort.

	3. Value not clear in the first 60 seconds. If the user can’t see the core value (a match, a lesson completed, a track played, a meeting joined) in their first session, the second session probably isn’t happening.

	4. Permission and privacy demands too early. Asking for location, contacts, payment or push before showing value is a measurable abandonment trigger. iOS push opt-in now hovers around 44%, down from a ~58% peak.

	5. Notification spam. Send 6–10 pushes a week from one brand and about 32% of users stop opening the app; over-messaging can raise uninstalls by up to ~50%. Frequency, not just relevance, matters.


### WHY USERS ABANDON APPS, RANKED BY IMPACT

| Causa de Abandono (Cause)                            | Impacto (%) | Categoria (Legenda de Cores) |
| ---                                                  | ---         | ---                          |
| **Performance: crashes, freezes, slow response**     | **53%**     | Technical floor (fix first)  |
| **Confusing or heavy UX / too many steps**           | **27%**     | Product / activation         |
| **Value not clear in the first session**             | **18%**     | Product / activation         |
| **Early permission & privacy demands**               | **16%**     | Trust & messaging            |
| **Notification spam (too many pushes)**              | **12%**     | Trust & messaging            |

--------------------------------------------------------------------------------
