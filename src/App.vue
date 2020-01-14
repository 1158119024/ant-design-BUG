<template>
  <div id="app">
    <a-table bordered :columns="columns" :components="components" :dataSource="data" @change="handleTableChange">
        <template v-slot:action>
        <a href="javascript:;">Delete</a>
        </template>
    </a-table>
  </div>
</template>

<script>
import Vue from 'vue';
import VueDraggableResizable from "vue-draggable-resizable";

Vue.component("vue-draggable-resizable", VueDraggableResizable);

const columns = [
  {
    title: "Date",
    dataIndex: "date",
    width: 200, sorter: true,
  },
  {
    title: "Amount",
    dataIndex: "amount",
    width: 100,
      sorter: (a, b) => a.amount - b.amount,
  },
  {
    title: "Type",
    dataIndex: "type",
    width: 100,
      filters: [
          {
              text: 'London',
              value: 'London',
          },
          {
              text: 'New York',
              value: 'New York',
          },
      ],
      filterMultiple: false,
      onFilter: (value, record) => record.type.indexOf(value) === 0,
      sorter: (a, b) => a.type.length - b.type.length,
      sortDirections: ['descend', 'ascend'],
  },
  {
    title: "Note",
    dataIndex: "note",
    width: 100
  },
  {
    title: "Action",
    key: "action",
    scopedSlots: { customRender: "action" }
  }
];
const data = [
  {
    key: 0,
    date: "2018-02-11",
    amount: 120,
    type: "income",
    note: "transfer"
  },
  {
    key: 1,
    date: "2018-03-11",
    amount: 243,
    type: "income",
    note: "transfer"
  },
  {
    key: 2,
    date: "2018-04-11",
    amount: 98,
    type: "income",
    note: "transfer"
  }
];
const draggingMap = {};
columns.forEach(col => {
  draggingMap[col.key] = col.width;
});
const draggingState = Vue.observable(draggingMap);
const ResizeableTitle = ({props, children}) => {
  let thDom = null;
  const { key, ...restProps } = props;
  const col = columns.find(col => {
    const k = col.dataIndex || col.key;
    return k === key;
  })||{};
  if (!col.width) {
    return <th {...restProps}>{children}</th>;
  }
  const onDrag = (x) => {
    draggingState[key] = 0;
    col.width = Math.max(x, 1);
  };

  const onDragstop = () => {
    draggingState[key] = thDom.getBoundingClientRect().width;
  };
  return (
    <th
      {...restProps}
      v-ant-ref={r => (thDom = r)}
      width={col.width}
      class="resize-table-th"
    >
      {children}
      <vue-draggable-resizable
        key={col.key}
        class="table-draggable-handle"
        w={10}
        x={draggingState[key] || col.width}
        z={1}
        axis="x"
        draggable={true}
        resizable={false}
        onDragging={onDrag}
        onDragstop={onDragstop}
      />
    </th>
  );
};

export default {
  name: "App",
  data() {
    this.components = {
      header: {
        cell: ResizeableTitle
      }
    };
    return {
      console: console,
      data,
      columns
    };
  },
  methods: {
      handleTableChange(){
          alert('handleTableChange');
      }
  }
};
</script>
<style>
.resize-table-th {
  position: relative;

}
.resize-table-th .table-draggable-handle {
    height: 100% !important;
    bottom: 0;
    left: auto !important;
    right: -5px;
    cursor: col-resize;
    touch-action: none;
  }
</style>
