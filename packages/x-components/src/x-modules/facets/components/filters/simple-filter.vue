<template>
  <RenderlessFilter
    v-slot="{ filter, clickFilter, cssClasses, isDisabled }"
    :class="cssClasses"
    :clickEvents="_clickEvents"
    :filter="filter"
    class="x-simple-filter"
  >
    <!--
      @slot The control element to render
      @binding {Filter} filter - The filter data
      @binding {() => void} clickFilter - Method that will invoke the needed actions after the user
      clicks the filter.
      @binding {Object} cssClasses - Object containing CSS classes to add to the button
      @binding {Boolean} isDisabled - True if the filter shouldn't be able to be selected by the
      user
    -->
    <slot
      v-bind="{
        filter,
        clickFilter,
        cssClasses,
        isDisabled
      }"
    >
      <button
        @click="clickFilter"
        :aria-checked="filter.selected.toString()"
        :class="cssClasses"
        :disabled="isDisabled"
        data-test="filter"
        role="checkbox"
      >
        <!--
          @slot The content to render inside the button
          @binding {Filter} filter - The filter data
        -->
        <slot :filter="filter" name="label">{{ filter.label }}</slot>
      </button>
    </slot>
  </RenderlessFilter>
</template>

<script lang="ts">
  import { SimpleFilter as SimpleFilterModel } from '@empathyco/x-types';
  import Vue from 'vue';
  import { Component, Prop } from 'vue-property-decorator';
  import { xComponentMixin } from '../../../../components';
  import { VueCSSClasses } from '../../../../utils/types';
  import { XEventsTypes } from '../../../../wiring/events.types';
  import { facetsXModule } from '../../x-module';
  import RenderlessFilter from './renderless-filter.vue';

  /**
   * Renders a simple filter, emitting the needed events when clicked.
   *
   * @public
   */
  @Component({
    components: { RenderlessFilter },
    mixins: [xComponentMixin(facetsXModule)]
  })
  export default class SimpleFilter extends Vue {
    /** The filter data to render. */
    @Prop({ required: true })
    public filter!: SimpleFilterModel;

    /**
     * Additional events, with their payload, to emit when the filter is clicked.
     *
     * @public
     */
    @Prop()
    public clickEvents?: Partial<XEventsTypes>;

    /**
     * The {@link XEventsTypes | events} to emit.
     *
     * @returns The events to emit when clicked.
     * @internal
     */
    protected get _clickEvents(): Partial<XEventsTypes> {
      return {
        UserClickedASimpleFilter: this.filter,
        ...this.clickEvents
      };
    }

    /**
     * Dynamic CSS classes to apply to the component.
     *
     * @returns The dynamic CSS classes to apply to the component.
     * @internal
     */
    protected get cssClasses(): VueCSSClasses {
      return {
        'x-simple-filter--is-selected': this.filter.selected
      };
    }
  }
</script>

<docs lang="mdx">
## Events

A list of events that the component will emit:

- [`UserClickedAFilter`](x-components.xeventstypes.userclickedafilter.md): the event is emitted
  after the user clicks the button, using the `filter` prop as its payload.
- [`UserClickedASimpleFilter`[(x-components.xeventstypes.userclickedasimplefilter.md): the event is
  emitted after the user clicks the button, using the `filter` prop as its payload.

## See it in action

This component renders a button, which on clicked emits the `UserClickedAFilter` and the
`UserClickedASimpleFilter` events. By default, it renders a `button` with the `filter.label`
property as text.

The `filter` prop is required. The `clickEvents` prop is optional and allows configuring the events
to emit on click.

```vue
<template>
  <SimpleFilter :filter="filter" />
</template>

<script>
  import { SimpleFilter } from '@empathyco/x-components/facets';

  export default {
    name: 'SimpleFilterTest',
    components: {
      SimpleFilter
    },
    data() {
      return {
        filter: {
          modelName: 'SimpleFilter',
          selected: false,
          id: 'category:shirts',
          value: 'category:shirts',
          facetId: 'category',
          totalResults: 10
        }
      };
    }
  };
</script>
```

### Playing with props

Configuring the events to emit when the filter is clicked.

```vue
<template>
  <SimpleFilter :clickEvents="{ UserClickedASimpleFilter: filter }" :filter="filter" />
</template>

<script>
  import { SimpleFilter } from '@empathyco/x-components/facets';

  export default {
    name: 'SimpleFilterTest',
    components: {
      SimpleFilter
    },
    data() {
      return {
        filter: {
          modelName: 'SimpleFilter',
          selected: false,
          id: 'category:shirts',
          value: 'category:shirts',
          facetId: 'category',
          totalResults: 10
        }
      };
    }
  };
</script>
```

### Rendering an input

You can change the rendered control using the default slot. Note that because of the current Vue
limitations, you must only render one single root node in this slot. There you will receive all the
data and methods needed:

```vue
<template>
  <SimpleFilter v-slot="{ filter, clickFilter, isDisabled, cssClasses }" :filter="filter">
    <label :class="cssClasses">
      <input :checked="filter.selected" type="checkbox" @change="clickFilter" />
      {{ filter.label }}
    </label>
  </SimpleFilter>
</template>

<script>
  import { SimpleFilter } from '@empathyco/x-components/facets';

  export default {
    name: 'SimpleFilterTest',
    components: {
      SimpleFilter
    },
    data() {
      return {
        filter: {
          modelName: 'SimpleFilter',
          selected: false,
          id: 'category:shirts',
          value: 'category:shirts',
          facetId: 'category',
          label: 'Shirts',
          totalResults: 10
        }
      };
    }
  };
</script>
```

### Changing default button content

You can change the content rendered by the default button using the `label` slot. There you will
receive the filter data.

```vue
<template>
  <SimpleFilter :filter="filter">
    <template #label="{ filter }">
      <img :src="`imgs/filters/${filter.id}.png`" />
      <span>{{ filter.label }}</span>
    </template>
  </SimpleFilter>
</template>

<script>
  import { SimpleFilter } from '@empathyco/x-components/facets';

  export default {
    name: 'SimpleFilterTest',
    components: {
      SimpleFilter
    },
    data() {
      return {
        filter: {
          modelName: 'SimpleFilter',
          selected: false,
          id: 'category:shirts',
          value: 'category:shirts',
          facetId: 'category',
          label: 'Shirts',
          totalResults: 10
        }
      };
    }
  };
</script>
```
</docs>
