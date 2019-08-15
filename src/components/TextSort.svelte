<style>

</style>

<div>
  <div class="sort-text">
    {#if (err)}
    <div class="row">
      <div class="col">
        <div class="alert alert-danger">{err}</div>
      </div>
    </div>
    {/if}
    <div class="row">
      <div class="col">
        <div class="form-group">
          <AceEditor
            bind:value={value}
            theme="clouds_midnight"
            width="100%"
            height="512"
            on:init={editorInit}
            on:input={onEditorChange}
          />
        </div>
      </div>
    </div>
    <div class="row">
      <div class="col">
        <div class="btn-group">
          <div class="btn btn-primary" on:click|preventDefault|capture={sort09AZaz}>Sort (09AZaz)</div>
          <div class="btn btn-primary" on:click|preventDefault|capture={sort09AZ}>Sort (09AZ)</div>
          <div class="btn btn-primary" on:click|preventDefault|capture={unique}>Unique</div>
          <div class="btn btn-primary" on:click|preventDefault|capture={clear}>Clear</div>
          <div class="btn btn-primary"
            disabled="{history.length === 0}"
            class:disabled={history.length === 0}
            on:click|preventDefault|capture={undo}
          >Undo</div>
        </div>
      </div>
    </div>
    <div class="row">
      <div class="col">
        <div class="form-check">
          <input class="form-check-input" id="checkbox_unique" bind:checked={config.unique} type="checkbox" />
          <label class="form-check-label" for="checkbox_unique">Unique</label>
        </div>
        <div class="form-check">
          <input class="form-check-input" id="checkbox_trim" bind:checked={config.trim} type="checkbox" />
          <label class="form-check-label" for="checkbox_trim">Trim</label>
        </div>
        <div class="form-check">
          <input class="form-check-input" id="checkbox_remove_empty" bind:checked={config.removeEmpty} type="checkbox" />
          <label class="form-check-label" for="checkbox_remove_empty">Remove Empty</label>
        </div>
        <div class="form-check">
          <input class="form-check-input" id="checkbox_reverse" bind:checked={config.reverse} type="checkbox" />
          <label class="form-check-label" for="checkbox_reverse">Reverse</label>
        </div>
        <div class="form-check">
          <input class="form-check-input" id="checkbox_toLowerCase" bind:checked={config.toLowerCase} type="checkbox" />
          <label class="form-check-label" for="checkbox_toLowerCase">toLowerCase</label>
        </div>
        <div class="form-check">
          <input class="form-check-input" id="checkbox_toUpperCase" bind:checked={config.toUpperCase} type="checkbox" />
          <label class="form-check-label" for="checkbox_toUpperCase">toUpperCase</label>
        </div>
      </div>
    </div>
  </div>
</div>

<script>
  import sqlFormatter from 'sql-formatter'
  import filter from 'lodash/filter'
  import orderBy from 'lodash/orderBy'
  import map from 'lodash/map'
  import uniq from 'lodash/uniq'
  import AceEditor from 'svelte-ace-editor';

  if (process.browser) {
    require('brace');
    // require('brace/ext/language_tools')
    require('brace/theme/clouds_midnight')
  }

  const seperatingCharacter = '\n';
  const config = {
    trim: true,
    removeEmpty: true,
    unique: true,
    reverse: false,
    toLowerCase: false,
    toUpperCase: false,
  };

  let err = '';
  let value = '';
  let history = [];
  let maxHistory = 10;

  let lines;
  $: lines = (value || '').split(seperatingCharacter);
  function setLines(newValue) {
    if (config.trim) {
      newValue = map(newValue, l => l.trim())
    }
    if (config.unique) {
      newValue = uniq(newValue)
    }
    if (config.removeEmpty) {
      newValue = filter(newValue, line => line !== '')
    }
    if (config.reverse) {
      newValue = (newValue || []).reverse()
    }
    if (config.toLowerCase) {
      newValue = map(newValue, s => s.toLowerCase())
    }
    if (config.toUpperCase) {
      newValue = map(newValue, s => s.toUpperCase())
    }
    value = newValue.join(seperatingCharacter) + '\n'
  }

  function editorInit () {
    // @todo
  }
  function pushHistory () {
    if (history[0] !== value) {
      if (history.unshift(value) > maxHistory) {
        history.length = maxHistory
      }
      history = history;
    }
  }
  function minify () {
    pushHistory()
    value = value || ''
    value = value.replace(whitespaceRegEx, ' ')
    value = value.replace(leftparenthesesRegEx, '(')
    value = value.replace(rightparenthesesRegEx, ')')
    value = value + '\n'
  }
  function clear () {
    pushHistory()
    value = ''
    value = value;
  }
  function sort09AZaz () {
    pushHistory()
    setLines(lines.sort());
  }
  function sort09AZ () {
    pushHistory()
    setLines(orderBy(lines, [line => line.toLowerCase()]));
  }
  function unique () {
    pushHistory()
    setLines(uniq(lines));
  }
  function undo () {
    value = history.shift()
    history = history;
    value = value;
  }
  function onEditorChange (newValue) {
    value = newValue.detail;
  }
</script>
