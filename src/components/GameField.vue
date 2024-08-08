<script setup>
import { reactive, ref } from 'vue';
import Figure from './Figure.vue';
import CellData from './CellData.vue';

const showData = ref(false);

const areaCellInfoData = reactive({
  cell: {
    figId: '',
    posX: '',
    posY: '',
    isRoot: '',
    rootCellPos: {y: '', x: ''},
    colFigIds: [],
  },
  figure: {
    id: '',
    rootPos: {y: '', x: ''},
    xyPos: {y: '', x: ''},
    selected: '',
    color: '',
  },
})

const rows = []; const cells = [];
for (let i=0; i<6; i++) { rows[i] = i;  cells[i] = i; }

const state = {
  status: 'unselected',
  selectedFigureId: Number,
}

const fig = (id, color, x, y) => {
  // create a figure
  return reactive({                             
    id: id,
    rootPos: {y: 5, x: x},
    xyPos: {y: y, x: x},
    selected: false,
    color: color,
  })
};

const redPalette = ["text-pink-500", "text-red-500"]

const colorsList = [
  redPalette,
  redPalette,
  redPalette,
]

// list of figures
const figures = [
  fig(0, colorsList[0][0], 0, 5),
  fig(1, colorsList[1][0], 0, 4),
  fig(2, colorsList[2][0], 0, 3),
]

const cellsInfo = [];
for (let y=0; y<6; y++) {
  cellsInfo[y] = [];
  for (let x=0; x<6; x++) {

    cellsInfo[y][x] = {                     
      figId: '',
      isRoot: false,
      rootCellPos: {y: 5, x: x},
      colFigIds: [],
    }

    if (y == 5) {
      cellsInfo[y][x].isRoot = true;
    }

  }
}

// define figures in cells
cellsInfo[3][0].figId = 2;
cellsInfo[4][0].figId = 1; 
cellsInfo[5][0].figId = 0; cellsInfo[5][0].colFigIds = [0, 1, 2]; cellsInfo[5][0].isRoot = true;


const checkNewPos = (y, x) => {
  // return true if new pos is correct
  if (y == 0 || y == 5) {  // || x == 0 || x == 5
    return true;
  }
  return false;
};


const figSelectToggle = (id, action) => {
  //
  if (action == 'select') {
    state.status = 'selected';
    state.selectedFigureId = id;
    figures[id].selected = true;
    figures[id].color = colorsList[id][1];
  }
  else if (action == 'unselect') {
    state.status = 'unselected';
    figures[id].selected = false;
    figures[id].color = colorsList[id][0];
  }
};


const hoverCell = (posY, posX) => {
  // hovered cell actions
  console.log(134123)
  showData.value = true;
  let cellHovered = cellsInfo[posY][posX];
  areaCellInfoData.cell.posX = posX;
  areaCellInfoData.cell.posY = posY;
  areaCellInfoData.cell.figId = cellHovered.figId;
  areaCellInfoData.cell.isRoot = cellHovered.isRoot;
  areaCellInfoData.cell.rootCellPos.y = cellHovered.rootCellPos.y;
  areaCellInfoData.cell.rootCellPos.x = cellHovered.rootCellPos.x;
  areaCellInfoData.cell.colFigIds = cellHovered.colFigIds;

  if (cellHovered.figId !== '') {
    let figure = figures[cellHovered.figId]
    areaCellInfoData.figure.id = figure.id
    areaCellInfoData.figure.rootPos.y = figure.rootPos.y
    areaCellInfoData.figure.rootPos.x = figure.rootPos.x
    areaCellInfoData.figure.xyPos.y = figure.xyPos.y
    areaCellInfoData.figure.xyPos.x = figure.xyPos.x
    areaCellInfoData.figure.selected = figure.selected
    areaCellInfoData.figure.color = figure.color
  }

}


const selectCell = (posY, posX) => {
  // selected cell actions

  let cellSelected = cellsInfo[posY][posX];

  // option 1  -  select figure  *********************************************************************************************************
  if ( cellSelected.figId !== '' & state.status == 'unselected' ) {  
    let rootCell = cellsInfo[cellSelected.rootCellPos.y][cellSelected.rootCellPos.x];
    let figId = rootCell.colFigIds[ rootCell.colFigIds.length - 1 ];
    figSelectToggle(figId, 'select');
    hoverCell(posY, posX);
    return 0;
  }

  // option 2  -  click on empty cell when no figure is selected yet **********************************************************************
  else if ( cellSelected.figId === '' & state.status == 'unselected' ) {
    hoverCell(posY, posX);
    return 0;
  }

  // option 3  -  trying to replace to the same column
  else if ( cellSelected.figId !== '' & state.status == 'selected' & posX == figures[state.selectedFigureId].xyPos.x ) {  
    figSelectToggle(state.selectedFigureId, 'unselect');
    hoverCell(posY, posX);
    return 0;
  }

  // option 4  -  replace figure ************************************************
  else if ( state.status == 'selected' & checkNewPos(posY, posX) ) {  
    let figId = state.selectedFigureId;
    let rootCell = cellsInfo[cellSelected.rootCellPos.y][cellSelected.rootCellPos.x];

    // if selected cell has some figures - count new Y of position when it will be not a root cell ***************************
    if (cellSelected.figId !== '') { 
      if (posY == 5) {
        posY = posY - rootCell.colFigIds.length 
      }
      else if (posY == 0) {
        posY = posY + rootCell.colFigIds.length 
      }
    };

    // add info to new pos and root
    cellsInfo[posY][posX].figId = figId;
    rootCell.colFigIds.push(figId);    

    // detect previous cell and root
    let prevPosY = figures[figId].xyPos.y; let prevPosX = figures[figId].xyPos.x;
    let rootPrevPosY = figures[figId].rootPos.y; let rootPrevPosX = figures[figId].rootPos.x;
    let cellPrev = cellsInfo[prevPosY][prevPosX];
    let rootCellPrev = cellsInfo[rootPrevPosY][rootPrevPosX];

    // erase info from previous cell and root
    cellPrev.figId = '';
    rootCellPrev.colFigIds.pop();   

    // change info in figure
    figures[figId].rootPos.y = 5;
    figures[figId].rootPos.x = posX;
    figures[figId].xyPos.y = posY;
    figures[figId].xyPos.x = posX;
    figSelectToggle(figId, 'unselect');

    hoverCell(posY, posX);

    return 0;
  }

};

const leaveGameField = () => {
  //
  showData.value = false;
}

</script>


<template>

  <section class="">
    <table class="mx-auto mt-5" @mouseleave="leaveGameField()">
      <tr class="h-10" v-for="y in rows">
        <td class="bg-green-100 border border-4 border-green-600 w-14 text-center hover:bg-green-200" 
          @click="selectCell(y, x)" @mouseenter="hoverCell(y, x)" v-for="x in cells">

          <!-- {{ cell }}-{{ row }} -->

            <div v-for="f in figures" :key="f.figId">
              <div v-if="y == f.xyPos.y & x == f.xyPos.x">
                <Figure :figClr=f.color />
              </div>
            </div>

        </td>
      </tr>
    </table>
  </section>

  <CellData :data=areaCellInfoData v-show="showData" />

</template>
