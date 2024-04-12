My Stuff
=======


# AI Related
https://github.com/noizu-labs-ml/genai
https://github.com/noizu-labs-ml/ex_llama
https://github.com/noizu-labs-ml/NoizuPromptLingo
https://github.com/noizu-labs-ml/elixir-openai
https://github.com/noizu-labs-ml/artificial_intelligence
https://github.com/noizu-labs-ml/elixir-weaviate
https://github.com/noizu-labs-ml/noizu-ops
https://github.com/noizu-labs/noizu-teams
https://github.com/noizu-labs/intellect

# Cache Management

## [FragmentedKeys](https://github.com/noizu/fragmented-keys)

## [FragmentedKeys4Java](https://github.com/noizu-labs/fragmented-keys-4java)
Java port of my PHP Frag Key library.
## [SwiftFragmentedKeys](https://github.com/noizu-labs/SwiftFragmentedKeys)
(Work In Progress) Implementation of Fragmented keys for managing data stored across various ios cache providers.


# Scaffolding 

## Elixir
https://github.com/noizu-labs/RuleEngine
https://github.com/noizu-labs/ElixirCore

### Newest

### Legacy
#### [Advanced Elixir Scaffolding](https://github.com/noizu-labs/advanced_elixir_scaffolding)
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

#### [SimplePoolAdvance](https://github.com/noizu-labs/SimplePoolAdvanced)
- Provide long lived process pools with advanced routing/fall over, default hooks and some other goodies. 
- Node Tenancy for Process once assigned 
- Syn (v2) or Registry based routing to avoid bottlenecks in GenServer or registered process routing. 
- Service/Node/Cluster (v2) monitoring
- Load Balancing of new workers (v2) assign to nodes lower workers/range ratios, and fewer errors per period. 

https://github.com/noizu-labs/KitchenSinkAdvanced

## PHP
### [PHP Domain Objects](https://github.com/noizu/domain-objects)


### [PhpConform: Gherkin/Cucumber phpunit extension](https://github.com/noizu/php-conform)
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
## [Elixir Github Client](https://github.com/noizu-labs/elixir-github)
An Elixir github client I implemented for letting AI agents access/insert tickets/comments on projects. 
Useful, ofcourse, wherever one needs to interop with Github from an Elixir codebase.

# Tooling/Libs

## `Elixir` [Assert Match](https://github.com/noizu-labs/assert_match)
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

## `embedded c` [Streaming Json Parser](https://github.com/noizu-labs/StreamingJsonParser)
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

## `embedded c`[TrieGen](https://github.com/noizu-labs/trie_gen)
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

## `liquibase` [Liquibase Extension](https://github.com/noizu-labs/liquibase-extensions)
More to see if I could than anything else this lib adds custom xml tags for creating/tearing down postgres enum types.

```xml
<changeSet author="you" id="create_enum">
  <customChange class="noizu.liquibase.postgres.enum.CreateOperation">
    <param name="enum" value="user_enum_name"/>
    <param name="values" value="value1,value2,value3"/>
  </customChange>
</changeSet>
```

## `enterprise architect` [EA Swift Extension](https://github.com/noizu-labs/ea-swift)
Basic UML -> Swift Code Generation Support extension for Sparx's Enterprise Architect.

# OPC (Other People's Code) Forks

## `Elixir` [Amnesia](https://github.com/noizu-labs/amnesia)
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

## `Elixir` `Google DataStore` [Diplomat](https://github.com/noizu-labs/diplomat)
Added support for switching between projects (useful for sychronizing environments) plus a few minor bug fixes/improvements.

## `Elixir` `SendGrid` [SendGrid](https://github.com/noizu-labs/sendgrid_elixir)
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


## `Elixir` [TZData](https://github.com/noizu-labs/tzdata)
As my IOT client's backend handles billions of tz operations per minute it became necessary to optimize the common tzdata library moving lookups into FastGlobal and now persistent_terms from ets. 

