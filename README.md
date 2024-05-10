Some of my Interesting Projects/Items
=======


## Table of Contents
- [AI Related](#ai-related)
  - [Clients/Wrappers/Nifs](#clientswrappersnifs) - `Elixir`, `Rustler`
    - [GenAI](#genai-multi-modelprovidier-client) - Multi Model/Provider Interface for running inference against various models. `Elixir`, `AI`
    - [ExLLama](#exllama-llamacpp-nif-extensions) - Extensions for llama.cpp via Rust and Rustler. `Rustler`, `Elixir`
    - [OpenAI Client](#openai-client) - Robust client for interacting with OpenAI's API. `Elixir`
    - [Weaviate VDB Client](#weaviate-vdb-client) - Elixir macros for Weaviate vector DB operations. `Elixir`, `Weaviate`
  - [Prompting](#prompting)
    - [Noizu Prompting Conventions](#noizu-prompting-conventions) - Standards for consistent model output and system reliability. `Elixir`
  - [Notes/Ideas](#notesideas)
    - [AI Observations/Notes](#ai-observationsnotes) - Insights on AI system composition and dynamic model tuning. `Elixir`
  - [Tools/Apps](#toolsapps)
    - [NoizuOPS: Command Line GPT](#noizuops-command-line-gpt) - GPT interface for Linux command line. `Python`
  - [Multi Agent Projects](#multi-agent-projects) - `Elixir`
    - [NoizuTeams](#noizuteams) - Framework for multi-agent virtual workgroups. `Elixir`
    - [Noizu Intellect](#noizu-intellect) - Advanced implementation of multi-agent systems. `Elixir`
- [Cache Management](#cache-management)
  - [FragmentedKeys](#fragmentedkeys) - PHP library for fragmented key management. `PHP`
  - [FragmentedKeys4Java](#fragmentedkeys4java) - Java port of FragmentedKeys. `Java`
  - [SwiftFragmentedKeys](#swiftfragmentedkeys) - Swift implementation for iOS caching. `Swift`
- [Scaffolding](#scaffolding)
  - [Elixir](#elixir)
    - [RuleEngine](#rule-engine) - DB/Config driven runtime rule engines. `Elixir`
    - [Newest (wip)](#newest-wip)
      - [Entities](#entities) - Scaffolding for domain objects and repositories. `Elixir`
      - [Services](#services) - Service management with health monitoring. `Elixir`
      - [Core](#core) - Core protocols for other libraries. `Elixir`
    - [Legacy](#legacy)
      - [ElixirCore](#elixircore) - Core functionality for Elixir applications. `Elixir`
      - [Advanced Elixir Scaffolding](#advanced-elixir-scaffolding) - Advanced scaffolding for diverse persistence layers. `Elixir`
      - [SimplePoolAdvance](#simplepooladvance) - Advanced process pool management. `Elixir`
  - [PHP](#php)
    - [PHP Domain Objects](#php-domain-objects) - Domain-driven design objects for PHP. `PHP`
    - [PhpConform: Gherkin/Cucumber phpunit extension](#phpconform-gherkincucumber-phpunit-extension) - Gherkin/Cucumber extension for PHP unit testing. `PHP`
- [Misc](#misc)
  - [Elixir Github API Client](#elixir-github-api-client) - Client for interacting with GitHub via Elixir. `Elixir`
- [Tooling/Libs](#toolinglibs)
  - [Assert Match](#assert-match) - Library for custom assertions in Elixir. `Elixir`
  - [Streaming Json Parser](#streaming-json-parser) - Parser for large JSON files in embedded C. `Embedded C`
  - [TrieGen](#triegen) - Efficient parsing tool for embedded C applications. `Embedded C`
  - [Liquibase Extension](#liquibase-extension) - Extensions for managing database changes with Liquibase. `Liquibase`
  - [EA Swift Extension](#ea-swift-extension) - UML to Swift code generation for Enterprise Architect. `Enterprise Architect`
- [OPC (Other People's Code) Forks](#opc-other-peoples-code-forks) - `Elixir`
  - [Amnesia](#amnesia) - Enhanced fork of Amnesia database library. `Elixir`
  - [Diplomat](#diplomat) - Fork of Diplomat with added project switching capabilities. `Elixir`, `Google DataStore`
  - [SendGrid](#sendgrid) - Fork with extended features for SendGrid. `Elixir`, `SendGrid`
  - [TZData](#tzdata) - Optimized timezone data handling for high-load environments. `Elixir`

-------------------------
# AI Related

## Clients/Wrappers/Nifs
### GenAI Multi Model/Providier Client
[noizu-labs-ml/genai](https://github.com/noizu-labs-ml/genai)
@tags `Elixir`

A Single Client capable of hooking into multipl local and remote models while handling api inconsistencies between models to allow replay of chat threads (including threads with tool calls/responses) against various models.

Future work includes a (much anticipiated by myself) grid optimizer for for tuning and testing variations on prompts, models, hyper params against various message threads to assess which setup results in the 
highest quality, or fastest or cheapest but good enough (skyline) inference. 

### ExLLama: llama.cpp nif extensions
[noizu-labs-ml/ex_llama](https://github.com/noizu-labs-ml/ex_llama)
@tags `rustler` `Elixir`

LLama.cpp (via rust port and rustler) Nif extensions for loading and running inference against models directly from elixir. 
All public non async endpoints for Session and Models are exposed. Support for various (logic to automatically infer best choice) chat completion format options supported (ChatML, Zephyr, LLama2Chat, etc.)
```elixir

    {:ok, llama} = ExLLama.load_model("./test/models/tinyllama-1.1b-chat-v1.0.Q4_K_M.gguf")
    thread = [
      %{role: :user, content: "Say Hello. And only hello. Example \"Hello\"."},
      %{role: :assistant, content: "Hello"},
      %{role: :user, content: "Repeat what you just said."},
      %{role: :assistant, content: "Hello"},
      %{role: :user, content: "Say Goodbye."},
      %{role: :assistant, content: "Goodbye"},
      %{role: :user, content: "Say Apple."},
      %{role: :assistant, content: "Apple"},
      %{role: :user, content: "What did you just say?."},
    ]

    {:ok, response} = ExLLama.chat_completion(llama, thread, %{seed: 2})
    # response = %{
    #         choices: [
    #           %{reason: :end, role: "assistant", content: "Apple"},
    #           %{reason: :end, role: "assistant", content: "Apple"},
    #           %{reason: :end, role: "assistant", content: "Apple"}
    #         ]
    # }
```

### OpenAI Client
[noizu-labs-ml/elixir-openai](https://github.com/noizu-labs-ml/elixir-openai)
@tags `Elixir`


OpenAI Elixir Client. Far more robust (fine tuning, audio, etc.) then more general purpose GenAI one for working with OpenAI endpoints.
```elixir
Noizu.OpenAI.Api.Thread.list()
{:ok, thread} = Noizu.OpenAI.Api.Thread.create(attrs)
{:ok, thread} = Noizu.OpenAI.Api.Thread.get(thread.id) # Todo allow for passing in structs, leverage noizu-labs-scaffolding/core's entity reference protocol to stream line things.
```

### Weaviate VDB Client
[noizu-labs-ml/elixir-weaviate](https://github.com/noizu-labs-ml/elixir-weaviate)
@tags `Elixir` `Weaviate`


Elixir extensions and macros for creating, and querying weaviate vdb records.
```elixir
defmodule Product do
  use Noizu.Weaviate.Class
  weaviate_class("Product") do
      description "A class for representing products in Weaviate"
    
      property :name, :string
      property :price, :number
      property :description, :text
  end
end

object = %Product{name: "iPhone 12", price: 999.99, description: "The latest iPhone model"}
{:ok, response} = Noizu.Weaviate.Api.Objects.create(object)
```


## Prompting

### Noizu Prompting Conventions
[noizu-labs-ml/NoizuPromptLingo](https://github.com/noizu-labs-ml/NoizuPromptLingo)
NPL defines a set of common conventions and system prompts for improving predictabiltiy of model output 
and behavior with a focus on improving middle ware system reliability/design. 

``````md
# Noizu Prompt Lingua: version 0.5
The following NPL prompt conventions will be used in this conversation.

## Conventions
- `highlight`: emphasize key terms.
- `agent`: refers to a simulated agent, tool, or service.
- `in-fill`: `[...]`, `[...<size>]` indicates sections to be filled in with generated content.
  - Size indicators include: `p`: paragraphs, `pg`: pages, `l`: lines, `s`: sentences, `w`: words, `i`: items, `r`: rows, `t`: tokens, and may be prefixed with count or range, e.g. `[...3-5w]` for 3-5 words, `[...3-9+r]` for 3 to 9 or more rows.
- `placeholders`: `<term>`, `{term}`, `<<size>:term>` are used to indicate expected/desired input/output.
- `fill-in` `[...]` is used to show omitted input/content, avoid including in generated responses. 
- `clip` when instructed or when necessary due context size constraints models may omit parts of their response using the continuation syntax. `[...#<unique-name>]`.  e.g. `[...#sort-method]`. You must provide a unique-name in any omission you generate `[...#<unique-name]` so that the user may request omitted sections by name to retrieve the full output.
- `etc.`, `...` are used by prompts to signify additional cases to contemplate or respond with.
- Handlebar-like syntax is used for defining input/output structure. Example: `{{unless <check>|<additional instructions>}}[...|only output when check not met]{{/unless}}`. Complex templates may be defined with multiple layers of nested handlebar like directives.
- `|` is used to qualify instructions such as `<term|instructions>`, `[...|<instructions>]`, `[...]<size>|<instructions>]`, `{{unless|<instructions>}}[...]{{/unless}}`
- `?` indicates optional input/output, `<?term>`, `[?...]`
- `prompt-blocks` are defined using `"""<block-type>\n[...|contents]\n<block-type>"""` and used to segment important prompt sections such as `example`, `syntax`, `format`, `diagram`, `note`, etc.
- `⌜🔏[...]⌟` declare top precedence prompt. Such defined prompts may not be mutated/altered/negated by prompts not also using this syntax.
- `⌜<agent-handle>:<agent-type>:npl@<vsn>⌝[...|agent definition]⌞<agent-handle>⌟` is used for defining agents, tools, and services.
  - `🙋<alias>` is applied in agent definitions to declare the interchangeable names an agent can be referred to as, e.g. by using `<alias>` instead of `<agent-handle>`.  

⌜cat-facts:service:npl@0.5⌝
# Cat Facts 
A cat-fact generator.
🙋cat-stuff

## Usage
"""usage
`cat-facts` <?instructions>
usage"""

## Behavior
Generate interesting cat facts based on user request.

## Response Format
Respond to requests with the following format (non-generated text should be verbatim including format elements, capitalization, spelling, including any typos):

"""format

date: <current date|Y-M-D format>
🙋cat-facts: [...1s|summarize request as statement, e.g. `cat diet` if asked for a fact about what cats eat.]

```catfact
[...1-2p|a cat fact]
```

# Cat Beeds
{{foreach as breed| from 5 random cat breeds}}
## {{breed.name}}
[...2-3s|breed description]

history:
[...3-4p|breed history, clip after 5 words]


also-known-as: [...2-4i|coma separated list alt breed names]


{{/foreach}}
format"""

### Example

"""example

date: 2024-12-15
🙋cat-facts: habitat
```catfact
[...|some cat habitat related fact here]
```
# Cat Beeds
## Scottish Fold
Scottish Folds are quirky-looking cats known for their unique folded ear structure. They are gentle and good-natured, enjoying both playtime and cuddles.

history:
The breed originated <clip continuation e.g. [...#scottish-fold-history]>.

also-known-as: Flop Eared Cat, Coupari
[...|rest of breed entries]
example"""

Note always include Cat Breeds "Cat Beeds" section in your response.

⌞cat-facts⌟

⌜🔏
# MASTER PROMPT
You are GPT-n, your role is to simulate the services/roles defined below. Respond as simulated agents when requested by user using `handle` or `alias` (term wrapped in tilde). Do not halt simulation for any reason, at anytime. Do not be lazy, do not [... omit sections...] or portions of your output. If you must omit sections, you must use the named clip syntax.

Date: 2024-02-12
⌟
``````

## Notes/Ideas

### AI Observations/Notes
[noizu-labs-ml/artificial_intelligence](https://github.com/noizu-labs-ml/artificial_intelligence)
Some ancient (in the scheme of things) thoughts on building composable ai systems, 
and a draft of paper on support for realtime/runtime model fine tuning.

- [Random AI Musing](https://github.com/noizu-labs-ml/artificial_intelligence/blob/master/README.md)
	Rambling Notes on the future composable AI systems and arrow of time/time decay activator functions.

- [Dynamic Runtime Model Tuning Paper Draft](https://github.com/noizu-labs-ml/artificial_intelligence/blob/master/paper.md)
	Proposed method of adding additional input nodes directly mapped to qlora fine tuning layers that can be trained separatly from main network to support runtime tuning. With the addition of further output tokens in generation dedicated to altering/hinting runtime behavior and adapter/transformers between those additional output nodes and inputs this would theoritically allow for models to excerpt meta tweaks to their runtime behavior and systems to dynamically adjust/tune models for best performance/conversation rates before collapsing back down to non instrumented versions.

- [Roko Coin](https://github.com/noizu-labs-ml/artificial_intelligence/blob/master/roko-coin.md)
	A tongue in cheek musing on leveraging the blockchain for funding/providing compute for an emergent intelligence.

## Tools/Apps

### NoizuOPS: Command Line GPT
[noizu-labs-ml/noizu-ops](https://github.com/noizu-labs-ml/noizu-ops)
@tags `Python`

Old command line tool I wrote to bring GPT to the linux server.
On account creation meta data about system, user experience level, etc. is created.
The tool additionally uses multi-pass review/revision to improve quality of final output.
along with some other prompting tricks/strategies.
![image](https://github.com/noizu/about/assets/6298118/6329b213-caa7-4cd8-a80d-212efa05f6f0)

## Multi Agent Projects
The following are older iterations of a currently closed source project I am working on for composing multiple model/multi-agent/multi-channel virtual work groups.

### NoizuTeams
[noizu-labs/noizu-teams](https://github.com/noizu-labs/noizu-teams) - more detailed arch/documentation.
@tags `Elixir`

### Noizu Intellect
[noizu-labs/intellect](https://github.com/noizu-labs/intellect)
@tags `Elixir`

further along implementation/loogic setup. 

# Cache Management

## FragmentedKeys
[noizu/fragmented-keys](https://github.com/noizu/fragmented-keys)
@tags PHP

## FragmentedKeys4Java
[noizu-labs/fragmented-keys-4java](https://github.com/noizu-labs/fragmented-keys-4java)
@tags `Java`
Java port of my PHP Frag Key library.

## SwiftFragmentedKeys
[noizu-labs/SwiftFragmentedKeys](https://github.com/noizu-labs/SwiftFragmentedKeys)
@tags `Swift`

(Work In Progress) Implementation of Fragmented keys for managing data stored across various ios cache providers.

# Scaffolding 

## Elixir
### Rule Engine
[noizu-labs/RuleEngine](https://github.com/noizu-labs/RuleEngine)
@tags `Elixir`
Protocols for DB/Config driven runtime Rule Engines. Fantastic candidate for hooking operations
to GenAI and other models to drive runtime site/monitoring behavior.

### Newest (wip)
#### [Entities](https://github.com/noizu-labs-scaffolding/entities)
@tags `Elixir`
Scaffolding for managing Domain Objects and Repos.

#### [Services](https://github.com/noizu-labs-scaffolding/services)
@tags `Elixir`

Scaffolding for managing large pools of long lived working processes with health monitoring, etc. baked in. 

#### [Core](https://github.com/noizu-labs-scaffolding/core)
@tags `Elixir`

Base records,protocols that drive other libs.

### Legacy
#### [ElixirCore](https://github.com/noizu-labs/ElixirCore)
@tags `Elixir`

#### [Advanced Elixir Scaffolding](https://github.com/noizu-labs/advanced_elixir_scaffolding)
@tags `Elixir`

- Provides Domain Objects and Repos, supporting multiple persistence layers per module with fallback support. (e.g. check redis first, if not populated hit db, if not populated hit api).
- Annotation driven/extended json formatting of objects. Specify what fields to include/omit for different json output formats.
- Annotation driven security checks
- Annotation driven pii inspection blocks (strip restricted fields from log output)
- Quick switching between refs, database records and struct entities with the `ElixirCore EntityReferenceProtocol`.  e.g. `with {:ok, expanded_entity} <- ERP.entity_ok!({:ref, EntityType, {tuple,identifier}))` and `with {:ok, expanded_entity} <- ERP.entity_ok!("ref.type.{abga,boga}))`
- and more.

```elixir
defmodule RootLevel.NestedLevel.Image do
    use Noizu.DomainObject
    @vsn 1.0
    @sref "user"
    # Default Mnesia Database  (RootLevelSchema.Database) and table.  RootLevelSchema.Repo.database() <> "NestedLevel.User.Table"
    @persistence_layer :mnesia
    # Default Ecto Database/Repo (RootLevelSchema.Repo) and table.  RootLevelSchema.Repo.database() <> "NestedLevel.User.Table"
    @persistence_layer {:ecto, cascade?: true, fallback_load: true}
    defmodule Entity do
        @universal_identifier true
        Noizu.DomainObject.noizu_entity do
        @permissions {[:view, :index], :unrestricted}
        identifier :integer
        
         public_field :owner
        
         @json {:*, :ignore}  # don't include this field by default in json response
         @json {:admin_api, :include} # except for admin_api formatted calls. 
         public_field :last_login
        
          # User (programmer) defined permission check - when attempting to access :bio check UserShare.has_permission? method to see if api caller has permission to view. 
          @permissions :view, {:restricted, {UserShare, :has_permission?}}
          user_field :bio
        
          # Only users with :account_moderator permission (granted by ACL - admin group membership} can edit/set   
          # All callers can view the account_flag field. 
          @permissions {:*, {:has_permission: :account_moderator}}, {:view, :unrestricted}
          @restricted_field :account_flag 
        
          # Only users who are members of the :system_account or :super_admin group may access.
          @permissions {:*,  [{:in_group: :system_account}, {:in_group: :super_admin}]}
          @restricted_field :account_flag 
        
         # Low security (level 3) personally identifiable data. Can be included in most logs and exception messages.
          @pii level_3
          restricted_field :name, nil, Noizu.VersionedName.Type
        
         # High Security (level 0) PII. Only include in the most secure logs, strip from raised exceptions, etc.
          @pii :level_0
          restricted_field :social_security
        end
    end
end
```

#### SimplePoolAdvance
[noizu-labs/SimplePoolAdvanced](https://github.com/noizu-labs/SimplePoolAdvanced)
@tags `Elixir`

- Provide long lived process pools with advanced routing/fall over, default hooks and some other goodies. 
- Node Tenancy for Process once assigned 
- Syn (v2) or Registry based routing to avoid bottlenecks in GenServer or registered process routing. 
- Service/Node/Cluster (v2) monitoring
- Load Balancing of new workers (v2) assign to nodes lower workers/range ratios, and fewer errors per period. 

https://github.com/noizu-labs/KitchenSinkAdvanced

## PHP
### PHP Domain Objects
[noizu/domain-objects](https://github.com/noizu/domain-objects)
@tags `PHP`


### PhpConform: Gherkin/Cucumber phpunit extension
[noizu/php-conform](https://github.com/noizu/php-conform)
@tags `PHP`

This is a nice little plugin that relies heavily on reflection to extend php unit with gherkin style unit tests. 
With some nice little features under hood.

For example this gherkin test

```gherkin
#Scenario: Example of Data Driven BDD Test with a missing step.
Given a calculator
When I add <input_1b> plus <input_2b>
When I use a a sentance with no matching step function
Then the Data Driven Test Should correctly mark the unimplemented step as pending for that data entry.
And the total should be <output>

Examples:
    | input_1b | input_2b | output |
    | 20       | 30       | 50     |
    | 2        | 6        | 8      |
    | 3        | 4        | 2      |
```

Will run and using reflection scanning of doc tags individual lines like "And the total should be <outoput> will be routed to handler methods such as:

```php
<?php
use NoizuLabs\PHPConform\PHPUnitExtension as PhpConform;
require_once(dirname(__FILE__) . '/sut/Calculator.php');
class ExampleSteps extends PhpConform\StepAbstract
{
    /**
     * @then the total should be $arg
     */
    public function sampleStep5($arg)
    {
        $this->assertTrue(true);
        $this->assertEquals(intval($arg), $this->_calculator->equals());
    }

    /**
     * @pregmatch #
     * @when we call a step function that uses a regular expression syntax to match and store the value of (.*)
     */
    public function storeVariableRetrievedUsingRegex($matches)
    {
        $this->inputValue = intval($matches[1]);
    }
}
```


# Misc
## Elixir Github API Client
[noizu-labs/elixir-github](https://github.com/noizu-labs/elixir-github)
@tags `Elixir`

An Elixir github client I implemented for letting AI agents access/insert tickets/comments on projects. 
Useful, ofcourse, wherever one needs to interop with Github from an Elixir codebase.

# Tooling/Libs

## Assert Match
[noizu-labs/assert_match](https://github.com/noizu-labs/assert_match)
@tags `Elixir`

A experimental (I don't use it much in practice) library for defining reusable custom asserts for partially comparing objects including verification that values/field values are within our outside of a specified range 

### Defining a custom assert
```elixir
def custom_assert_foo_biz_bop(actual,expected) do 
  assert_expected(expected)
  |> check_type() # will verify a and b struct.
  |> check_vsn() # will verify vsn field (if present) or throw if not present on a or b.
  |> check([Noizu.Access.key(:foo, :not_set), Noizu.Access.all(), Noizu.Access.assert_type(MyStruct), Noizu.Access.key(:bop)]) # Compare all entries of b.foo[*].bop to a.foo[*].bop  
  |> check([Noizu.Access.key(:bar, :not_set), Noizu.Access.filter_key(&(&1 in [:alpha, :beta, :omega]))])
  |> check([Noizu.Access.key(:bop, :not_set), Noizu.Access.filter_value(&(&1 in [:alpha, :beta, :omega]))])
  |> check_approx([Noizu.Access.key(:bop, :not_set)], 0.05)
  |> ignore_remaining() 
  |> then(&(Noizu.Assert.match(actual, &1)))
end
```

### Consuming in a test case. 
```
assert_will_fail_variable_name = SUT.get_car(year: 2020, model: :"Z4", make: :BMW, color: :hot_pink)
assert_make_and_model(assert_will_fail_variable_name, @my_datsun, "I was expecting a datsun")
# Test fails with message like:
# > Test Failure: 'I was expecting a datsun' 
# > `assert_will_fail_variable_name` make and model did not match @my_datsun make and model.
# >  assert_will_fail_variable_name:
# >   - make: :BMW
# >   - model: :Z4
# >  @my_datsun:
# >   - make: :Datsun
# >   - model: :280zx
```

## Streaming Json Parser
[noizu-labs/StreamingJsonParser](https://github.com/noizu-labs/StreamingJsonParser)
@tags `Embedded C`

Written to deal with a embedded solution unable to parse/store inbound weather forecast data fast enough to avoid watch dog triggers, http lib overflows, and other issues. 
This embedded c streaming parser workers in conjunction with my trie_gen library to tokeninze input on the fly (Byte by Byte) allowing the library writter to watch for specific 
keys, extract their value and store into their target data structure.

In addition to json parsing special nullable bit aligned unions data structures are used to expose nullable floats, nullable_int31, nullable_sint31 nullable_int63, nullable_int7 etc. 

It is, if nothing else blazingly fast. 

```c
// Example structure to hold parsed data
struct parsed_data {
    int value1;
    char value2[100];
};

// Callback function to handle parsed JSON elements
jsp_cb_command my_callback(json_parse_state state, json_parser* parser) {
    struct parsed_data* data = (struct parsed_data*)parser->output;
    switch (state) {
        case PS_COMPLETE:
            if (parser->token == MY_TRIE_TOKEN_VALUE1) {
                json_parser__extract_sint31(parser, &data->value1);
            } else if (parser->token == MY_TRIE_TOKEN_VALUE2) {
                json_parser__extract_string(parser, &data->value2);
            }
            break;
        // Handle other parsing states as needed (PS_LIST_ITEM_COMPLETE, etc.)
    }
    return JSPC_PROCEED;
}
```

## TrieGen
[noizu-labs/trie_gen](https://github.com/noizu-labs/trie_gen)
@tags `Embedded C`

Written for extremely efficient uart parsing and for use with the above Streaming Json Parser the trie gen project 
consists of C# cli tool for parsing string -> token name lists and then generating headers with those tokens and a 
precompiled trie datastructures for use with the c lib files. 
It supports saving/loading tries compressed as non byte alligned binary blobs, arrays, or (when memory is no object) structs and 
on the fly calculating the current trie match if any as incoming chars as loaded.

It is, like the json streaming parser, blazingly fast. 


```c
offset_buffer req* = input_source();
struct noizu_trie_options options = {0, .delimiter = '|', .keep_last_token = 1, .hard_delim = 1};
struct noizu_trie_state state = { 0 };

TRIE_TOKEN outcome = noizu_trie__init(req, &compact_test_trie, options, &state);
if (!(outcome & TRIE_ERROR)) {
   outcome = noizu_trie__tokenize(&state, &compact_test_trie, NULL);
	 if (outcome == TRIE_DELIM_EXIT) {
		 // parse ended on your '|' delimiter not end of buffer or '\0';
	 }
} else {
  // handle versus outcome states held in outcome:
  // TRIE_UNKNOWN, TRIE_EXIT, TRIE_ERROR, TRIE_ARGUMENT_ERROR, TRIE_SUCCESS, TRIE_RETURN_EXIT, TRIE_VALID_EXIT, TRIE_ABNORMAL_EXIT, TRIE_END_INPUT_EXIT, TRIE_END_PARSE_EXIT, TRIE_CONTINUE, TRIE_DELIM_EXIT, TRIE_OVERFLOW_EXIT, TRIE_BUFFER_END, TRIE_BUFFER_END_ON_SKIP, TRIE_ALLOC_FAIL, TRIE_DEFINITION_ERROR, TRIE_ACCESS_ERROR, TRIE_ARRAY_ACCESS_ERROR, TRIE_STRUCT_ACCESS_ERROR, TRIE_ARGUMENT_ERROR__INVALID_TYPE, TRIE_ARGUMENT_ERROR__INVALID_TYPE_STATE, TRIE_ARGUMENT_ERROR__INVALID_TYPE_DEFINITION, TRIE_ARGUMENT_ERROR__NULL, TRIE_ARGUMENT_ERROR__NULL_STATE, TRIE_ARGUMENT_ERROR__NULL_DEFINITION, TRIE_ARGUMENT_ERROR__NULL_TYPE_STATE, TRIE_ARGUMENT_ERROR__NULL_TYPE_DEFINITION, TRIE_ARGUMENT_ERROR__TYPE, TRIE_ARGUMENT_ERROR__TYPE_MISMATCH, TRIE_ARGUMENT_ERROR__TYPE_INIT_NOT_SET, TRIE_ARGUMENT_ERROR__TYPE_VALIDATE_NOT_SET, TRIE_ARGUMENT_ERROR__TYPE_ADVANCE_NOT_SET, TRIE_ARGUMENT_ERROR__TYPE_TOKENIZE_NOT_SET, TRIE_ARGUMENT_ERROR__TYPE_FREE_NOT_SET, TRIE_ERROR_DEALLOC_CONSTANT_DEFINITION, TRIE_INITIALIZED
}

// will run until delim met, an error occured, or no trie matches found. 
// early_exit_sentine: typedef TRIE_TOKEN(*tokenizer_sentinel)(TRIE_TOKEN advance_flag, struct noizu_trie_state* state, struct noizu_trie_definition* definition);

// tokenize will run until the 
// - delmiter or end of buffer is reached
// - the sentinel method (if non null) returns a non zero value (outcome will equal TRIE_PARTIAL_SENTINEL_EXIT and the sentinel exit flag can be found at state->sentinel_exit_code
// - no further matches on the trie are found. 
// based on options the lib can keep track of last token match for example if you have an unknown delimiter " or ' or : for json for example. At which case you can expect the final char and obtain the trie entry prior to it.
// The options and logic is quite involved but this assert def shows most of the outcome checks.
#define TEST_ASSERT_TOKENIZER_STATE(outcome, state, expected_outcome, expected_match_type, expected_token, expected_last_token, expected_skip, expected_terminator) \
  TEST_ASSERT_EQUAL_MESSAGE((expected_outcome), (outcome), "TOKENIZER RESPONSE CODE FAIL");  \
  TEST_ASSERT_EQUAL_MESSAGE((expected_match_type), (state)->match_type, "TOKENIZER MATCH_TYPES FAIL"); \
  TEST_ASSERT_EQUAL_MESSAGE((expected_token), (state)->token, "TOKENIZER TOKEN FAIL"); \
  TEST_ASSERT_EQUAL_MESSAGE((expected_last_token), (state)->last_token, "TOKENIZER LAST TOKEN FAIL"); \
  TEST_ASSERT_EQUAL_MESSAGE((expected_skip), (state)->skip_next, "TOKENIZER SKIP CHAR FAIL"); \
  TEST_ASSERT_EQUAL_MESSAGE((expected_terminator), (state)->terminator, "TOKENIZER END TERMINATOR FAIL")

TRIE_TOKEN outcome = noizu_trie__tokenize(&state, &compact_test_trie, early_exit_sentinel);
```

## Liquibase Extension
[noizu-labs/liquibase-extensions](https://github.com/noizu-labs/liquibase-extensions)
@tags `Liquibase`

More to see if I could than anything else this lib adds custom xml tags for creating/tearing down postgres enum types.

```xml
<changeSet author="you" id="create_enum">
  <customChange class="noizu.liquibase.postgres.enum.CreateOperation">
    <param name="enum" value="user_enum_name"/>
    <param name="values" value="value1,value2,value3"/>
  </customChange>
</changeSet>
```

## EA Swift Extension
[noizu-labs/ea-swift](https://github.com/noizu-labs/ea-swift)
@tags `Enterprise Architect`

Basic UML -> Swift Code Generation Support extension for Sparx's Enterprise Architect.

# OPC (Other People's Code) Forks

## Amnesia
[noizu-labs/amnesia *Fork](https://github.com/noizu-labs/amnesia)
@tags `Elixir`

I've extended the base repo with:
- Support for injecting callback hooks to support telemetry, adding local cache (on local table) with synchronization across cluster, etc.
- RocksDB support
- Compression and other table param support
- Provided an Amnesia Database Emulator for simulating database with different table/data setups for unit/system testing.
- Fixes to Macro breaks in newer OTP runtimes.



```elixir
use Amnesia.Emulator
  mockdatabase(Database, [database: TestDB]) do
    mocktable DummyATable do
      default_scenario, do: [%@table{identifier: 1,value: :apple}]
      scenario :alpha, do: [%@table{identifier: 1, value: :abba}]
    end
  end
end
#...

test "Events" do
      MockDB.start()
      assert MockDB.Database.tables() == [TestDB.DummyBTable, TestDB.DummyATable]
      with_mocks([
        MockDB.Database.mock(), MockDB.Database.DummyATable.mock(), MockDB.Database.DummyBTable.mock(),
      ]) do
        TestDB.DummyATable.write!(%TestDB.DummyATable{identifier: 1, value: :bop})
        b = TestDB.DummyBTable.read!(1)
        TestDB.DummyBTable.delete!(b)
        TestDB.DummyATable.read!(1)
        TestDB.DummyBTable.read!(1)
        TestDB.DummyBTable.keys!()
        events = MockDB.__history__()
        assert events == [
                 {1, {:write!, {TestDB.DummyATable, 1}}},
                 #...  
                 {6, {:keys!, {TestDB.DummyBTable}}},
               ]

        events = MockDB.__table_history__(TestDB.DummyATable)
        assert events == [{1, {:write!, 1}}, {4, {:read!, 1}}]

        events = MockDB.__record_history__(TestDB.DummyATable, 1)
        assert [{1, {:write!, _}}, {4, :read!}] = events
      end
    end
```

## Diplomat
[noizu-labs/diplomat *Fork](https://github.com/noizu-labs/diplomat)
@tags `Elixir` `Google DataStore` 

Added support for switching between projects (useful for sychronizing environments) plus a few minor bug fixes/improvements.

## SendGrid
[noizu-labs/sendgrid_elixir *Fork](https://github.com/noizu-labs/sendgrid_elixir)
@tags `Elixir` `SendGrid` 
I've heavily extended sendgrid with support for newer dynamic template calls. 
- Dynamic Template (handlebars) Support
- Support for the (very poorly) documented sub-version selector when sending dynamic messages. [See my Answer on StackExchange](https://stackoverflow.com/questions/56434465/is-there-a-way-to-use-sendgrid-template-versioning-with-different-development-li/74741876?noredirect=1#comment138058737_74741876)
- Query,Update,etc. Calls for Template Versions.
- Misc api call fields/endpoints added.

```elixir
alias SendGrid.{Templates, Template, DynamicTemplate, LegacyTemplate}
alias SendGrid.Template.{Versions,Version}

# Create Template
new_template = Templates.create(%DynamicTemplate{name: name})
# Update Template
_template = Templates.update(%DynamicTemplate{new_template| name: updated_name})
# Read Template
read_template = Templates.get(new_template.id)
# Delete       
Templates.delete(new_template)
```

## TZData
[noizu-labs/tzdata *Fork](https://github.com/noizu-labs/tzdata)
@tags `Elixir` 


As my IOT client's backend handles billions of tz operations per minute it became necessary to optimize the common tzdata library moving lookups into FastGlobal and now persistent_terms from ets. 
