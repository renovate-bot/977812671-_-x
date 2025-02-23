<template>
  <component
    :is="animation"
    v-if="hasFiltersToRender"
    tag="ul"
    class="x-filters"
    :class="cssClasses"
    data-test="base-filters"
  >
    <li
      v-for="filter in renderedFilters"
      :key="filter.id"
      class="x-filters__item"
      data-test="base-filters-item"
    >
      <!--
        @slot (Required) Filter content
            @binding {Filter} filter - Search-types filter data.
      -->
      <slot :filter="filter" />
    </li>
  </component>
</template>

<script lang="ts">
  import Vue from 'vue';
  import { mixins } from 'vue-class-component';
  import { Component, Prop } from 'vue-property-decorator';
  import { xComponentMixin } from '../../../../components';
  import { VueCSSClasses } from '../../../../utils/types';
  import { facetsXModule } from '../../x-module';
  import FiltersInjectionMixin from './filters-injection.mixin';

  /**
   * Renders a list with a list item per each
   * {@link @empathyco/x-types#BooleanFilter | BooleanFilter} in the filters prop array.
   * Each list item has a scoped slot, passing the filter as slot prop.
   *
   * @public
   */
  @Component({
    mixins: [xComponentMixin(facetsXModule)]
  })
  export default class FiltersList extends mixins(FiltersInjectionMixin) {
    /**
     * Animation component that will be used to animate the base filters.
     *
     * @public
     */
    @Prop({ default: 'ul' })
    protected animation!: Vue | string;

    /**
     * It handles if the filters should be rendered.
     *
     * @returns True if there are filters.
     *
     * @public
     */
    protected get hasFiltersToRender(): boolean {
      return this.renderedFilters?.length > 0;
    }

    /**
     * Checks if at least one filter is selected.
     *
     * @returns True if at least one filter is selected. False otherwise.
     * @internal
     */
    protected get hasSelectedFilters(): boolean {
      return !!this.renderedFilters?.some(filter => filter.selected);
    }

    /**
     * Dynamic CSS classes for the root element of this component.
     *
     * @returns An object containing the dynamic CSS classes and a boolean indicating if they should
     * be added or not.
     */
    protected get cssClasses(): VueCSSClasses {
      return {
        'x-filters--has-selected-filters': this.hasSelectedFilters
      };
    }
  }
</script>

<style lang="scss" scoped>
  .x-filters {
    list-style-type: none;
  }
</style>

<docs lang="mdx">
## Examples

Renders a list with a list item per each filter in the filters prop array. Each list item has a
scoped slot, passing the filter as slot prop.

### Important

The component has two ways of receive the filters list, it can be injected by another component or
be send it as a prop. If the component doesnt have a parent component that receive and exposed a
filters list to their children, it is mandatory to send it as prop.

### Basic usage

Using default slot:

```vue
<FiltersList :filters="filters">
  <template #default="{ filter }">
    <p>{{ filter.label }}</p>
  </template>
</FiltersList>
```

Using default slot abbreviated syntax:

```vue
<FiltersList :filters="filters" v-slot="{ filter }">
  <p>{{ filter.label }}</p>
</FiltersList>
```

> **Using injection**: It can receive the filters list by injection. It only works if it has a
> parent component that receives and exposes the filters list. Using the injection, It is not
> necessary to send the prop to the child components, it has to be send it in the parent component ,
> the rest of components will inject this list.

```vue
<SlicedFilters :filters="filters">
  <FiltersList v-slot="{ filter }">
    <p>{{ filter.label }}</p>
  </FiltersList>
</SlicedFilters>
```
</docs>
