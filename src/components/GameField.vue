<script setup>
import { reactive, ref } from 'vue';
import Figure from './Figure.vue';
import CellData from './CellData.vue';


const rows = []; const cells = [];
for (let i=0; i<16; i++) { rows[i] = i;  };
for (let i=0; i<12; i++) { cells[i] = i; };

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


const redPalette = ["text-pink-500", "text-red-500"];
const bluePalette = ["text-indigo-500", "text-blue-500"];

const colorsList = [
  redPalette, redPalette, redPalette, redPalette, redPalette, redPalette, redPalette, redPalette, redPalette, redPalette, redPalette, redPalette, 
  redPalette, redPalette, redPalette,
  bluePalette, bluePalette, bluePalette, bluePalette, bluePalette, bluePalette, bluePalette, bluePalette, bluePalette, bluePalette, bluePalette, bluePalette, 
  bluePalette, bluePalette, bluePalette,
];


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


// list of figures
const figures = [
  fig(0, colorsList[0][0], 0, 15, 15, 'red', 0),
  fig(1, colorsList[1][0], 0, 14, 15, 'red', 0),
  fig(2, colorsList[2][0], 0, 13, 15, 'red', 0),
  fig(3, colorsList[3][0], 0, 12, 15, 'red', 0),
  fig(4, colorsList[4][0], 0, 11, 15, 'red', 0),
  fig(5, colorsList[5][0], 0, 10, 15, 'red', 0),
  fig(6, colorsList[6][0], 0, 9, 15, 'red', 0),
  fig(7, colorsList[7][0], 0, 8, 15, 'red', 0),
  fig(8, colorsList[8][0], 0, 7, 15, 'red', 0),
  fig(9, colorsList[9][0], 0, 6, 15, 'red', 0),
  fig(10, colorsList[10][0], 0, 5, 15, 'red', 0),
  fig(11, colorsList[11][0], 0, 4, 15, 'red', 0),
  fig(12, colorsList[12][0], 0, 3, 15, 'red', 0),
  fig(13, colorsList[13][0], 0, 2, 15, 'red', 0),
  fig(14, colorsList[14][0], 0, 1, 15, 'red', 0),

  fig(15, colorsList[15][0], 11, 0, 0, 'blue', 0),
  fig(16, colorsList[16][0], 11, 1, 0, 'blue', 0),
  fig(17, colorsList[17][0], 11, 2, 0, 'blue', 0),
  fig(18, colorsList[18][0], 11, 3, 0, 'blue', 0),
  fig(19, colorsList[19][0], 11, 4, 0, 'blue', 0),
  fig(20, colorsList[20][0], 11, 5, 0, 'blue', 0),
  fig(21, colorsList[21][0], 11, 6, 0, 'blue', 0),
  fig(22, colorsList[22][0], 11, 7, 0, 'blue', 0),
  fig(23, colorsList[23][0], 11, 8, 0, 'blue', 0),
  fig(24, colorsList[24][0], 11, 9, 0, 'blue', 0),
  fig(25, colorsList[25][0], 11, 10, 0, 'blue', 0),
  fig(26, colorsList[26][0], 11, 11, 0, 'blue', 0),
  fig(27, colorsList[27][0], 11, 12, 0, 'blue', 0),
  fig(28, colorsList[28][0], 11, 13, 0, 'blue', 0),
  fig(29, colorsList[29][0], 11, 14, 0, 'blue', 0),
]

const cellsInfo = [];
for (let y=0; y<16; y++) {
  cellsInfo[y] = [];
  for (let x=0; x<12; x++) {

    let rootY = (y < 8) ? 0 : 15;

    cellsInfo[y][x] = {                     
      figId: '',
      isRoot: false,
      rootCellPos: {y: rootY, x: x},
      colFigIds: [],
    };

    if ( [0, 15].includes(y) ) {
      cellsInfo[y][x].isRoot = true;
    };

    if (y == 15) {
      cellsInfo[y][x].posIdRed = x;
      cellsInfo[y][x].posIdBlue = x+12;
    } else if (y == 0) {
      cellsInfo[y][x].posIdRed = 11-x+12;
      cellsInfo[y][x].posIdBlue = 11-x;
    }

  };
};

// define figures in cells  -  red player
// cellsInfo[3][0].figId = 2;
// cellsInfo[4][0].figId = 1; 
// cellsInfo[5][0].figId = 0; cellsInfo[5][0].colFigIds = [0, 1, 2];
cellsInfo[15][0].figId = 0; cellsInfo[15][0].colFigIds = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14];
cellsInfo[14][0].figId = 1; cellsInfo[13][0].figId = 2; cellsInfo[12][0].figId = 3; cellsInfo[11][0].figId = 4; cellsInfo[10][0].figId = 5;
cellsInfo[9][0].figId = 6; cellsInfo[8][0].figId = 7; cellsInfo[7][0].figId = 8; cellsInfo[6][0].figId = 9; cellsInfo[5][0].figId = 10;
cellsInfo[4][0].figId = 11; cellsInfo[3][0].figId = 12; cellsInfo[2][0].figId = 13; cellsInfo[1][0].figId = 14;

// // define figures in cells  -  blue player
// cellsInfo[0][5].figId = 3; cellsInfo[0][5].colFigIds = [3, 4, 5];
// cellsInfo[1][5].figId = 4; 
// cellsInfo[2][5].figId = 5; 
cellsInfo[0][11].figId = 15; cellsInfo[0][11].colFigIds = [15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29];
cellsInfo[1][11].figId = 16; cellsInfo[2][11].figId = 17; cellsInfo[3][11].figId = 18; cellsInfo[4][11].figId = 19; 
cellsInfo[5][11].figId = 20; cellsInfo[6][11].figId = 21; cellsInfo[7][11].figId = 22; cellsInfo[8][11].figId = 23; 
cellsInfo[9][11].figId = 24; cellsInfo[10][11].figId = 25; cellsInfo[11][11].figId = 26; cellsInfo[12][11].figId = 27; 
cellsInfo[13][11].figId = 28; cellsInfo[14][11].figId = 29; 


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


// const checkNewPos = (y, x) => {
//   // return true if new pos is correct
//   if (y == 0 || y == 5) {  // || x == 0 || x == 5
//     return true;
//   }
//   return false;
// };


const selectCell = (posY, posX) => {
  // selected cell actions

  let cellSelected = cellsInfo[posY][posX];

  // option 1  -  select figure  *********************************************************************************************************
  if ( cellSelected.figId !== '' & state.status == 'unselected' ) {  
    let rootCell = cellsInfo[cellSelected.rootCellPos.y][cellSelected.rootCellPos.x];
    let figId = rootCell.colFigIds[ rootCell.colFigIds.length - 1 ];

    // unable select not current player figure
    console.log('figures[figId] =', figures[figId])
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
  // else if ( cellSelected.figId !== '' & state.status == 'selected' & posX == figures[state.selectedFigureId].xyPos.x ) {  
    
  //   console.log('same col case')
  //   console.log('cellSelected=', cellSelected)
  //   let figId = state.selectedFigureId;
  //   let newPosId = (figures[figId].player == 'red') ? cellSelected.posIdRed : cellSelected.posIdBlue;
  //   console.log(figures[figId].posId, newPosId)
  //   if (newPosId == figures[figId].posId) {
  //     console.log('impossible due to same col case')
  //     figSelectToggle(state.selectedFigureId, 'unselect');
  //     hoverCell(posY, posX);
  //     return 1;
  //   }    
  //   console.log('check for same col case is ok')

  // }

  // option 4  -  replace figure ************************************************
  // else if ( state.status == 'selected' & checkNewPos(posY, posX) ) {  
  else if ( state.status == 'selected' ) {  
    let figId = state.selectedFigureId;
    let rootCell = cellsInfo[cellSelected.rootCellPos.y][cellSelected.rootCellPos.x];

    let adaptPosY = cellSelected.rootCellPos.y;    /////
    let adaptPosX = cellSelected.rootCellPos.x     /////
    let adaptCellSelected = cellsInfo[adaptPosY][adaptPosX]; /////

    // unable to move backward
    let newPosId = (figures[figId].player == 'red') ? adaptCellSelected.posIdRed : adaptCellSelected.posIdBlue;
    console.log(figures[figId].posId, newPosId)
    if (newPosId <= figures[figId].posId) {
      figSelectToggle(state.selectedFigureId, 'unselect');
      hoverCell(adaptPosY, adaptPosX);
      return 1;
    }

    // if selected cell has some figures - count new Y of position when it will be not a root cell ***************************
    if (adaptCellSelected.figId !== '') { 

      // unable moving to another player ocuppied cell
      if ( figures[adaptCellSelected.figId].player !== state.currentPlayer ) { 
        return 1;
       }

      if (adaptPosY == 15) {
        adaptPosY = adaptPosY - rootCell.colFigIds.length 
      }
      else if (adaptPosY == 0) {
        adaptPosY = adaptPosY + rootCell.colFigIds.length 
      }
    };

    // add info to new pos and root
    cellsInfo[adaptPosY][adaptPosX].figId = figId;
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
    figures[figId].rootPos.y = (adaptPosY < 8) ? 0: 15;
    figures[figId].rootPos.x = adaptPosX;
    figures[figId].xyPos.y = adaptPosY;
    figures[figId].xyPos.x = adaptPosX;
    figSelectToggle(figId, 'unselect');
    
    figures[figId].posId = (figures[figId].player == 'red') ? rootCell.posIdRed : rootCell.posIdBlue;
    state.currentPlayer = (state.currentPlayer == 'red') ? 'blue' : 'red';
    
    hoverCell(adaptPosY, adaptPosX);

    return 0;
  }

};

const leaveGameField = () => {
  //
  showData.value = false;
}

</script>


<template>

<div class="grid grid-cols-2 w-max">

  <div class="ml-5">
    <table class="mx-auto mt-5" @mouseleave="leaveGameField()">
      <tr class="h-10" v-for="y in rows">
        <td class="bg-green-100 border border-4 border-green-600 w-14 text-center hover:bg-green-200" 
          @click="selectCell(y, x)" @mouseenter="hoverCell(y, x)" v-for="x in cells">
            <div v-for="f in figures" :key="f.figId">
              <div v-if="y == f.xyPos.y & x == f.xyPos.x">
                <Figure :figClr=f.color />
              </div>
            </div>
        </td>
      </tr>
    </table>
  </div>

  <div>
    <CellData :data=areaCellInfoData :showD=showData />
  </div>

</div>

</template>
