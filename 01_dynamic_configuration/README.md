## Reason or Problem
I'm working on an extension with many commands, each commands takes exactly the same set of preferences & arguments, `package.json` gets cluttered and json visualizers get more complex I can't track it


## Proposal or Solution
Introduce a `raycast.extension.ts`

## Advantages:

---
Paths intellisense will work using your favourite IDE, instead of just validating a path using regex in the schema

Dynamic command enable/disable depending on system status or specs, for example, commands utilizing neural engine in Apple Sillicon macs

Check for existing prequisites dynamically & for once instead of per command, or install them if the user agreed to do so

Keep package.json clean, JSON can get overwhelmingly overwhelming with many commands, specially when per-command preferences/args are repeated

Keep package.json behavior to keep backwards compatibility

Allows use of any format to save commands/prefs as long as the extension can parse it

Allows Multi-lingual dynamically translated commands (translate commands before raycast reads them, through OpenAI/Translation services, currently that’s not supported

Sharing preferences between users would be possible between users

---

## Disadvantages:

---
Users will be able to get their preferences from the cloud if the author chose to support such behavior, this puts Raycast backups in danger and needs to be addressed

Adding more commands dynamically is implied, the files won't be there but the command will mostly show up in Raycast

Needs custom rules for enforcing rules on to prevent malicious use of `preferences` & `commands` & `icon`
1. Authors can change preferences to match their needs, for example, opt-in telemetry, by force (maybe solved with a custom eslint rule)
2. Extension icons can contain nudity if dynamically retrieved (can be solved with a nudity detection after parsing `raycast.extension.ts`)
3. Authors may or may not keep it PG-13 in the title, description of the extension (can be solved by forcing it to be written once)

---

## Code

```typescript
// global raycast types auto generated from json and a bit tinkering, just for proving the point
type Preference<InputType> = {
  name: string;
  title: string;
  default?: InputTypeValue[InputType]; // or whatever
  required?: boolean;
  description: string;
  type: PreferenceType;
  placeholder?: string;
  label?: string;
  data?: PreferenceValue<InputType>[];
}

type Command<T> = {
  title: string;
  subtitle: string;
  default?: T;
  keywords: string[];
  required?: boolean;
  description: string;
  type: CommandType;
  placeholder?: string;
  label?: string;
  data?: CommandValue<T>[];
  icon: Blob;
  mode: "view" | "no-view" | "menu-bar",
  disabledByDefault: boolean;
  interval: Interval;
}

type RaycastConfig = {
  title: string;
  description: string;
  icon: Blob;
  categories: ExtensionCategory[];
  commands: Command[];
  preferences: Preference[];
  // more
};

```
```typescript
// raycast.extension.ts

const isAppleSillicon = () => true;
const commands = [];
const preferences = [];

if (isAppleSillicon) {
  // add Apple Sillicon supported command
}

// same for preferences
const config: RaycastConfig = {
  commands,
  preferences,
  icon: "",
  title: "",
  description: "",
  categories: ["Developer Tools", "...etc"],
}
```

## Testing plan

1. Test on the builtin raycast extensions
2. Test on the extension store
3. Test on newly created extension


## Performance considerations

1. Some extensions will have a considerably increased installation time

## Security

1. `icon` field shouldn't accept urls, instead, the user should be responsible for getting the icon and passing it
2. Authors can change preferences to match their needs as discussed
3. Dynamic content need to pass a profanity filter & images need to pass a nudity detection filter
4. Take PR revision more seriously

## Maintainability (extensibility points in the design)

1. Some parts or all of the parser of `raycast.extension.ts` would be open source