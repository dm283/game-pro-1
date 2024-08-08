<script setup>
import { reactive, ref } from 'vue';
import Figure from './Figure.vue';
import CellData from './CellData.vue';


const rows = []; const cells = [];
for (let i=0; i<6; i++) { rows[i] = i;  cells[i] = i; }

const state = {
  status: 'unselected',
  selectedFigureId: '',
  currentPlayer: 'red',
}

const showData = ref(false);

const areaCellInfoData = reactive({
  cell: {
    figId: '',
    posX: '',
    posY: '',
    isRoot: '',
    rootCellPos: {y: '', x: ''},
    colFigIds: [],
    posIdRed: '',
    posIdBlue: '',
  },
  figure: {
    id: '',
    rootPos: {y: '', x: ''},
    xyPos: {y: '', x: ''},
    posId: '',
    selected: '',
    color: '',
    player: ''
  },
  state: {
    status: state.status,
    selectedFigureId: state.selectedFigureId,
    currentPlayer: state.currentPlayer,
  }
})


const fig = (id, color, x, y, rootY, player, posId) => {
  // create a figure
  return reactive({                             
    id: id,
    rootPos: {y: rootY, x: x},
    xyPos: {y: y, x: x},
    selected: false,
    color: color,
    player: player,
    posId: posId,
  })
};

const redPalette = ["text-pink-500", "text-red-500"];
const bluePalette = ["text-indigo-500", "text-blue-500"];

const colorsList = [
  redPalette,
  redPalette,
  redPalette,
  bluePalette,
  bluePalette,
  bluePalette,
];

// list of figures
const figures = [
  fig(0, colorsList[0][0], 0, 5, 5, 'red', 0),
  fig(1, colorsList[1][0], 0, 4, 5, 'red', 0),
  fig(2, colorsList[2][0], 0, 3, 5, 'red', 0),
  fig(3, colorsList[3][0], 5, 0, 0, 'blue', 0),
  fig(4, colorsList[4][0], 5, 1, 0, 'blue', 0),
  fig(5, colorsList[5][0], 5, 2, 0, 'blue', 0),
]

const cellsInfo = [];
for (let y=0; y<6; y++) {
  cellsInfo[y] = [];
  for (let x=0; x<6; x++) {

    let rootY = (y <= 2) ? 0 : 5;

    cellsInfo[y][x] = {                     
      figId: '',
      isRoot: false,
      rootCellPos: {y: rootY, x: x},
      colFigIds: [],
    };

    if ( [0, 5].includes(y) ) {
      cellsInfo[y][x].isRoot = true;
    };

    if (y == 5) {
      cellsInfo[y][x].posIdRed = x;
      cellsInfo[y][x].posIdBlue = x+6;
    } else if (y == 0) {
      cellsInfo[y][x].posIdRed = 5-x+6;
      cellsInfo[y][x].posIdBlue = 5-x;
    }

  };
};

// define figures in cells  -  red player
cellsInfo[3][0].figId = 2;
cellsInfo[4][0].figId = 1; 
cellsInfo[5][0].figId = 0; cellsInfo[5][0].colFigIds = [0, 1, 2];
// define figures in cells  -  blue player
cellsInfo[0][5].figId = 3; cellsInfo[0][5].colFigIds = [3, 4, 5];
cellsInfo[1][5].figId = 4; 
cellsInfo[2][5].figId = 5; 


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
    state.selectedFigureId = '';
    figures[id].selected = false;
    figures[id].color = colorsList[id][0];
  }
};


const hoverCell = (posY, posX) => {
  // hovered cell actions
  showData.value = true;
  let cellHovered = cellsInfo[posY][posX];

  areaCellInfoData.state.status = state.status;
  areaCellInfoData.state.selectedFigureId = state.selectedFigureId;
  areaCellInfoData.state.currentPlayer = state.currentPlayer;

  areaCellInfoData.cell.posX = posX;
  areaCellInfoData.cell.posY = posY;
  areaCellInfoData.cell.figId = cellHovered.figId;
  areaCellInfoData.cell.isRoot = cellHovered.isRoot;
  areaCellInfoData.cell.rootCellPos.y = cellHovered.rootCellPos.y;
  areaCellInfoData.cell.rootCellPos.x = cellHovered.rootCellPos.x;
  areaCellInfoData.cell.colFigIds = cellHovered.colFigIds;
  areaCellInfoData.cell.posIdRed = cellHovered.posIdRed;
  areaCellInfoData.cell.posIdBlue = cellHovered.posIdBlue;

  if (cellHovered.figId !== '') {
    let figure = figures[cellHovered.figId]
    areaCellInfoData.figure.id = figure.id
    areaCellInfoData.figure.rootPos.y = figure.rootPos.y
    areaCellInfoData.figure.rootPos.x = figure.rootPos.x
    areaCellInfoData.figure.xyPos.y = figure.xyPos.y
    areaCellInfoData.figure.xyPos.x = figure.xyPos.x
    areaCellInfoData.figure.selected = figure.selected
    areaCellInfoData.figure.color = figure.color
    areaCellInfoData.figure.player = figure.player
    areaCellInfoData.figure.posId = figure.posId
  }

}


const selectCell = (posY, posX) => {
  // selected cell actions

  let cellSelected = cellsInfo[posY][posX];

  // option 1  -  select figure  *********************************************************************************************************
  if ( cellSelected.figId !== '' & state.status == 'unselected' ) {  
    let rootCell = cellsInfo[cellSelected.rootCellPos.y][cellSelected.rootCellPos.x];
    let figId = rootCell.colFigIds[ rootCell.colFigIds.length - 1 ];

    // unable select not current player figure
    if (figures[figId].player !== state.currentPlayer) {
      return 1;
    }
    
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

    // unable to move backward
    let newPosId = (figures[figId].player == 'red') ? cellSelected.posIdRed : cellSelected.posIdBlue;
    console.log(figures[figId].posId, newPosId)
    if (newPosId <= figures[figId].posId) {
      console.log('backward')
      return 1;
    }

    // if selected cell has some figures - count new Y of position when it will be not a root cell ***************************
    if (cellSelected.figId !== '') { 

      // unable moving to another player ocuppied cell
      if ( figures[cellSelected.figId].player !== state.currentPlayer ) { 
        return 1;
       }

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
    figures[figId].rootPos.y = (posY <= 2) ? 0: 5;
    figures[figId].rootPos.x = posX;
    figures[figId].xyPos.y = posY;
    figures[figId].xyPos.x = posX;
    figSelectToggle(figId, 'unselect');
    
    figures[figId].posId = (figures[figId].player == 'red') ? rootCell.posIdRed : rootCell.posIdBlue;
    state.currentPlayer = (state.currentPlayer == 'red') ? 'blue' : 'red';
    
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

  <CellData :data=areaCellInfoData :showD=showData />

</template>
