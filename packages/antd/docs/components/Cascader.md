# Cascader

> 联级选择器

## Markup Schema 案例

```tsx
import React from 'react'
import { Cascader, FormItem, FormButtonGroup, Submit } from '@formily/antd'
import { createForm, onFieldReact } from '@formily/core'
import { FormProvider, createSchemaField } from '@formily/react'
import { LoadingOutlined } from '@ant-design/icons'
import { action } from 'mobx'

const SchemaField = createSchemaField({
  components: {
    Cascader,
    FormItem,
  },
})

const useAddress = (pattern: Formily.Core.Types.FormPathPattern) => {
  const transform = (data = {}) => {
    return Object.entries(data).reduce((buf, [key, value]) => {
      if (typeof value === 'string')
        return buf.concat({
          label: value,
          value: key,
        })
      const { name, code, cities, districts } = value
      const _cities = transform(cities)
      const _districts = transform(districts)
      return buf.concat({
        label: name,
        value: code,
        children: _cities.length
          ? _cities
          : _districts.length
          ? _districts
          : undefined,
      })
    }, [])
  }
  onFieldReact(pattern, (field) => {
    field.setComponentProps({
      suffixIcon: <LoadingOutlined />,
    })
    fetch('//unpkg.com/china-location/dist/location.json')
      .then((res) => res.json())
      .then(
        action((data) => {
          field.setDataSource(transform(data))
          field.setComponentProps({
            suffixIcon: undefined,
          })
        })
      )
  })
}

const form = createForm({
  effects: () => {
    useAddress('address')
  },
})

export default () => (
  <FormProvider form={form}>
    <SchemaField>
      <SchemaField.String
        name="address"
        title="地址选择"
        x-decorator="FormItem"
        x-component="Cascader"
        x-component-props={{
          style: {
            width: 240,
          },
        }}
      />
    </SchemaField>
    <FormButtonGroup>
      <Submit onSubmit={console.log}>提交</Submit>
    </FormButtonGroup>
  </FormProvider>
)
```

## JOSN Schema 案例

```tsx
import React from 'react'
import { Cascader, FormItem, FormButtonGroup, Submit } from '@formily/antd'
import { createForm, onFieldReact } from '@formily/core'
import { FormProvider, createSchemaField } from '@formily/react'
import { LoadingOutlined } from '@ant-design/icons'
import { action } from 'mobx'

const SchemaField = createSchemaField({
  components: {
    Cascader,
    FormItem,
  },
})

const transformAddress = (data = {}) => {
  return Object.entries(data).reduce((buf, [key, value]) => {
    if (typeof value === 'string')
      return buf.concat({
        label: value,
        value: key,
      })
    const { name, code, cities, districts } = value
    const _cities = transformAddress(cities)
    const _districts = transformAddress(districts)
    return buf.concat({
      label: name,
      value: code,
      children: _cities.length
        ? _cities
        : _districts.length
        ? _districts
        : undefined,
    })
  }, [])
}

const useAsyncDataSource = (url: string, transform: (data: any) => any) => (
  field
) => {
  field.setComponentProps({
    suffixIcon: <LoadingOutlined />,
  })
  fetch(url)
    .then((res) => res.json())
    .then(
      action((data) => {
        field.setDataSource(transform(data))
        field.setComponentProps({
          suffixIcon: undefined,
        })
      })
    )
}

const form = createForm()

const schema = {
  type: 'object',
  properties: {
    address: {
      type: 'string',
      title: '地址选择',
      'x-decorator': 'FormItem',
      'x-component': 'Cascader',
      'x-component-props': {
        style: {
          width: 240,
        },
      },
      'x-reactions': [
        '{{useAsyncDataSource("//unpkg.com/china-location/dist/location.json",transformAddress)}}',
      ],
    },
  },
}

export default () => (
  <FormProvider form={form}>
    <SchemaField
      schema={schema}
      scope={{ useAsyncDataSource, transformAddress }}
    />
    <FormButtonGroup>
      <Submit onSubmit={console.log}>提交</Submit>
    </FormButtonGroup>
  </FormProvider>
)
```

## 纯 JSX 案例

```tsx
import React from 'react'
import { Cascader, FormItem, FormButtonGroup, Submit } from '@formily/antd'
import { createForm, onFieldReact } from '@formily/core'
import { FormProvider, Field } from '@formily/react'
import { LoadingOutlined } from '@ant-design/icons'
import { action } from 'mobx'

const useAddress = (pattern: Formily.Core.Types.FormPathPattern) => {
  const transform = (data = {}) => {
    return Object.entries(data).reduce((buf, [key, value]) => {
      if (typeof value === 'string')
        return buf.concat({
          label: value,
          value: key,
        })
      const { name, code, cities, districts } = value
      const _cities = transform(cities)
      const _districts = transform(districts)
      return buf.concat({
        label: name,
        value: code,
        children: _cities.length
          ? _cities
          : _districts.length
          ? _districts
          : undefined,
      })
    }, [])
  }
  onFieldReact(pattern, (field) => {
    field.setComponentProps({
      suffixIcon: <LoadingOutlined />,
    })
    fetch('//unpkg.com/china-location/dist/location.json')
      .then((res) => res.json())
      .then(
        action((data) => {
          field.setDataSource(transform(data))
          field.setComponentProps({
            suffixIcon: undefined,
          })
        })
      )
  })
}

const form = createForm({
  effects: () => {
    useAddress('address')
  },
})

export default () => (
  <FormProvider form={form}>
    <Field
      name="address"
      title="地址选择"
      decorator={[FormItem]}
      component={[
        Cascader,
        {
          style: {
            width: 240,
          },
        },
      ]}
    />
    <FormButtonGroup>
      <Submit onSubmit={console.log}>提交</Submit>
    </FormButtonGroup>
  </FormProvider>
)
```

## API

参考 https://ant.design/components/cascader-cn/