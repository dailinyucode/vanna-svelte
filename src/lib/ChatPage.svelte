<script lang="ts">
  import type { MessageContents } from "./types";
  import UserMessage from "./UserMessage.svelte";
  import AgentResponse from "./AgentResponse.svelte";
  import Text from "./Text.svelte";
  import SlowReveal from "./SlowReveal.svelte";
  import InChatButton from "./InChatButton.svelte";
  import Title from "./Title.svelte";
  import DataFrame from "./DataFrame.svelte";
  import Plotly from "./Plotly.svelte";
  import Thinking from "./Thinking.svelte";
  import SidebarToggleButton from "./SidebarToggleButton.svelte";
  import GreenButton from "./GreenButton.svelte";
  import TextInput from "./TextInput.svelte";
    import Error from "./Error.svelte";
    import CodeBlock from "./CodeBlock.svelte";
    import Feedback from "./Feedback.svelte";
    import SqlInput from "./SqlInput.svelte";
    import DrawInput from "./DrawInput.svelte";
    import SqlFix from "./SqlFix.svelte"

  export let suggestedQuestions: MessageContents | null = null;
  export let messageLog: MessageContents[];
  export let newQuestion: (question: string) => void;
  export let rerunSql: (id: string) => void;
  export let clearMessages: () => void;
  export let onUpdateSql: (sql: string) => void;
  export let question_asked: boolean;
  export let marked_correct: boolean | null;
  export let auto_fix_sql: boolean | null
  export let redraw_chart: boolean;
  export let thinking: boolean;
  export let onRedrawChart: (chart_instructions: string) => void
  export let onAutoFixSql: ()=> void

</script>

<!-- Content -->
<div class="relative h-screen w-full lg:pl-64">
    <div class="py-10 lg:py-14">
  
      <Title />
  
      {#if suggestedQuestions && suggestedQuestions.type == 'question_list' && !question_asked}
        <AgentResponse>
          <Text>
              <Text>{suggestedQuestions.header}</Text>
              <br/>
            {#each suggestedQuestions.questions as question}
                <InChatButton message={question} onSubmit={newQuestion} />
            {/each}
          </Text>
        </AgentResponse>
      {/if}

      <ul class="mt-16 space-y-5">
        {#each messageLog as message}
          {#if message.type === 'user_question'}
            <UserMessage message={message.question} />
          {:else if message.type === 'sql'}
              <AgentResponse>
                <Text>
                  <CodeBlock>
                    <SlowReveal text={message.text} />
                  </CodeBlock>
                </Text>
              </AgentResponse>
          {:else if message.type === 'question_list'}
              <AgentResponse>
              <Text>
                  <Text>{message.header}</Text>
                  <br/>
                  {#each message.questions as question}
                      <InChatButton message={question} onSubmit={newQuestion} />
                  {/each}
              </Text>
            </AgentResponse>
          {:else if message.type === 'df'}
              <AgentResponse>
              <DataFrame id={message.id} df={message.df} />
            </AgentResponse>
          {:else if message.type === 'plotly_figure'}
              <AgentResponse>
                <Plotly fig={message.fig} />
              </AgentResponse>
              <AgentResponse>
                  <InChatButton message="描述你的图" onSubmit={() => redraw_chart = true} />
              </AgentResponse>
              <AgentResponse>
                <Text>结果正确吗?</Text>
                {#if marked_correct === null}
                  <InChatButton message="正确" onSubmit={() => marked_correct = true} />
                  <InChatButton message="错误" onSubmit={() => marked_correct = false} />
                {/if}
              </AgentResponse>
              {#if marked_correct === true}
                <UserMessage message="成功完成一次查询" />
              {:else if marked_correct === false}
                <UserMessage message="错误，结果是错误的。" />
              {/if}              
          {:else if message.type === 'error'}
              <AgentResponse>
              <Error message={message.error} />
            </AgentResponse>
          {:else if message.type === 'sql_error'}
              <AgentResponse>
                  <Error message={JSON.stringify(message.error)} />
                  <InChatButton message="ai自动修正" onSubmit={onAutoFixSql} />
                  <InChatButton message="手动修正" onSubmit={() => auto_fix_sql = false} />
              </AgentResponse>
          {:else if message.type === 'question_cache'}
              <UserMessage message={message.question} />
              <AgentResponse>
              <Text>
                <CodeBlock>
                  {message.sql}
                </CodeBlock>
              </Text>
              </AgentResponse>
              <AgentResponse>
                <DataFrame id={message.id} df={message.df} />
              </AgentResponse>
              <AgentResponse>
                <Plotly fig={message.fig} />
              </AgentResponse>
          {:else if message.type === 'user_sql'}
              <UserMessage message="编写你的sql">
                <SqlInput onSubmit={onUpdateSql} />
              </UserMessage>
          {:else if message.type === 'redraw_chart'}
              <UserMessage message="编写你的描述">
                 <DrawInput onSubmit={onRedrawChart} />
              </UserMessage>
          {:else if message.type === 'fix_sql'}
              <UserMessage message="手动修改sql">
                  <SqlFix old_sql="{message.old_sql}" onSubmit={onUpdateSql} />
              </UserMessage>
          {:else}
              <AgentResponse>
                <Text>
                  {JSON.stringify(message)}
                </Text>
              </AgentResponse>
          {/if}
        {/each}
      
        {#if thinking}
          <Thinking />
        {/if}
  
      </ul>
    </div>
  
    <!-- Search -->
    <footer class="max-w-4xl mx-auto sticky bottom-0 z-10 p-3 sm:py-6">
  
      <SidebarToggleButton />
  
      {#if question_asked}
        <GreenButton message="新问题" onSubmit={clearMessages} />
        {#each messageLog as msg}
          {#if msg.type === 'question_cache'}
            <GreenButton message="重新运行sql" onSubmit={() => msg.type === 'question_cache' ? rerunSql(msg.id) : undefined } />
          {/if}
        {/each}
      {:else}
        <TextInput onSubmit={newQuestion} />
      {/if}
  
    </footer>
    <!-- End Search -->
  </div>
  <!-- End Content -->