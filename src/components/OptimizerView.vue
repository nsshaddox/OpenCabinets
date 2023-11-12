<template>
  <div id="optimizer" class="sheet-flex"> 
    <h4>Material: {{ this.material }} x{{ this.roots.length }}</h4> 
  </div>
</template>

<script>
  import { TYPES, INDEX } from './types'

  export default {
    name: "OptimizerView",
    props: {
      templates: {
        type: Array,
        default: () => [],
      },
    },
    data() {
      return {
        partsList: [],
        groupList: [],
        isOptimized: false,
        roots: [],
        numRecurse: 0,
        TYPES: TYPES,
        debug: false,
        material: 'Melamine',
      }
    },
    created () {
      this.partsList = this.createPartsList();
      this.groupList = this.createGroupList();
      this.optimize()

      if (this.debug){
        console.log("\n==== DONE =====")
        console.log("Completed Tree: ", this.root)
        console.log("Number of remaining parts:", this.partsList.length)
        console.log("Parts:", this.partsList)
      }
      
    },
    mounted() {
      const optimizer = document.getElementById('optimizer')
      const rect = optimizer.getBoundingClientRect()
      const spacer = 400
      let itr = 0
      for (let root of  this.roots) {
        //create a new div
        const sheet = document.createElement('div')
        sheet.className = 'sheet'
        sheet.innerHTML = '<br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br>'
        optimizer.appendChild(sheet)
        this.createBox(root, rect.left, rect.top + (itr * spacer) + 40)
        itr += 1
      }
    },
    methods: {
      optimize() {
        while (this.groupList.length > 0){

          let root = this.createNode(48, 96, TYPES.ROOT, []);
          console.log(this.groupList.length)
          this.recurse(root, this.groupList.length - 1)
          this.roots.push(root)
          console.log(this.groupList.length)
        }
      },

      recurse(root, index) {
        //at this point, this.partsList should be empty
        if (this.debug) {
          console.log("\n==== RECURSE CALLED ====")
          console.log("numRecurse: ", ++this.numRecurse)
          console.log("root:", root)
          console.log("remaining parts: ")
          for (let group of this.groupList) {
            for (let part of group.parts) {
              console.log(part)
            }
          }
        }


        if (this.numRecurse > 20) { return }
        // console.log("partsList:", this.partsList)
        if (this.groupList.length == 0) { 
          if (this.debug) console.log("FALLOFF")
          root.type = TYPES.FALLOFF
          return 
        }

        let length = this.groupList[index].length
        let width = this.groupList[index].width

        //===== HORIZONTAL CUT =====
        if (root.type == TYPES.V_CUT) {
          if (this.debug) console.log("Creating Horizontal Cut")
          let newIndex = index
          let remainingWidth = root.width
          let isGood = (length == root.length && width <= remainingWidth)
          
          while (isGood) {
            for (let part of this.groupList[newIndex].parts) {
              if (this.debug) console.log("Adding: ", part[INDEX.NAME])
              let partNode = this.createNode(part[INDEX.WIDTH], part[INDEX.LENGTH], TYPES.H_CUT, [])
              partNode.name = part[INDEX.NAME]
              root.nodes.push(partNode)
            }
            
            remainingWidth -= width
            newIndex--

            if (newIndex < 0) { break }

            width = this.groupList[newIndex].width
            length = this.groupList[newIndex].length
            isGood = (length == root.length && width <= remainingWidth)
          }

          // create new list for partsList removing all used parts
          this.groupList = [...this.groupList.slice(0, newIndex + 1), ...this.groupList.splice(index + 1)]
          
          // create new node for the remining ammount, call recurse, and add it to the nodes list
          let rootNode = this.createNode(remainingWidth, root.length, TYPES.ROOT, [])
          
          if (this.debug) console.log("rootNode: ", rootNode)
          
          this.recurse(rootNode, this.groupList.length - 1) //remaining part
          root.nodes.push(rootNode)
          
        } 
        
        //===== VERTICAL CUT =====
        else {
          if (this.debug) console.log("Creating Vertical Cut")
          let i = index

          // find the index of the part that will fit
          while( i > -1 ) {
            length = this.groupList[i].length
            width = this.groupList[i].width
            if (length <= root.length && width <= root.width) { break }
            i--
          }
          
          // FALL OFF PIECE!!!
          if (i == -1) {
            if (this.debug) console.log("FALLOFF PIECE")
            root.type = TYPES.FALLOFF
            return // early return 
          }
          
          let vertNode = this.createNode(root.width, length, TYPES.V_CUT, [])
          let rootNode = this.createNode(root.width, root.length-length, TYPES.ROOT, [])
          
          if (this.debug){
            console.log("vertNode: ", vertNode)
            console.log("rootNode: ", rootNode)
          }
          
          this.recurse(vertNode, i) // matching part
          this.recurse(rootNode, this.groupList.length - 1)// remaining part
          
          root.nodes.push(vertNode)
          root.nodes.push(rootNode)
          
        }
      },
      
      createPartsList() {
        let partsList = []
        console.log(this.templates)
        for (var template of this.templates) {
          for(var part of template.Components) {
            if (part[INDEX.QUAN] > 0 && part[INDEX.MATERIAL] === this.material && part[INDEX.THICK] === 0.75) {
              for (let i = 0; i < part[INDEX.QUAN]; i++ ){
                partsList.push(part);
              }
            }
          }
        }

        partsList.sort((a, b) => {
          // AREA
          // return a[INDEX.WIDTH] * a[INDEX.LENGTH] - b[INDEX.WIDTH] * b[INDEX.LENGTH]

          // LENGTH > WIDTH
          if (a[INDEX.LENGTH] === b[INDEX.LENGTH]) {
            return a[INDEX.WIDTH] - b[INDEX.WIDTH]
          }
          return a[INDEX.LENGTH] - b[INDEX.LENGTH] 
        })

        return partsList
      },

      createGroupList() {
        if (this.partsList.length == 0) return []
        
        let remainingWidth = 48
        let prevLength = this.partsList[this.partsList.length - 1][INDEX.LENGTH]
        let groupList = [this.createGroup(0, prevLength, [])]

        while(this.partsList.length > 0) {
          let part = this.partsList.pop()
          remainingWidth -= part[INDEX.WIDTH]

          if (remainingWidth >= 0 && prevLength == part[INDEX.LENGTH]) {
            //groupList[groupList.length - 1].push(part)
            groupList[0].parts.push(part)
            groupList[0].width += part[INDEX.WIDTH]
          } else {
            //create a new group and add the next part to it
            remainingWidth = 48 - part[INDEX.WIDTH]
            //groupList.push([part])
            groupList.unshift(this.createGroup(part[INDEX.WIDTH], part[INDEX.LENGTH], [part]))
          }

          prevLength = part[INDEX.LENGTH]
        }

        if (this.debug) console.log("groupList:", groupList)

        groupList.sort((a, b) => {
          // AREA
          return a.width * a.length - b.width * b.length
        })

        return groupList
      },

      createNode(width, length, type, nodes){
        return {
          "width": width,
          "length": length,
          "type": type,
          "nodes": nodes,
          "name": "",
        }
      },

      createGroup(width, length, part) {
        return {
          "width": width,
          "length": length,
          "parts": part,
        }
      },

      createBox(root, x, y) {
        if (root.width === 0 || root.length === 0) {return}

        const boxElement = document.getElementById('optimizer')
        const newBox = document.createElement('div')

        if (this.debug) console.log(x,y,boxElement,newBox)

        newBox.style.position = 'absolute'
        newBox.textContent = "" + root.width + "," + root.length + " " + root.name
        newBox.style.left = x + 'px'
        newBox.style.top = y + 'px'
        newBox.style.width = root.length*8 + 'px'
        newBox.style.height = root.width*8 + 'px'
        newBox.style.border = '1px solid black'
        newBox.style.backgroundColor = 'lightgray' // Adjust color as needed

        boxElement.appendChild(newBox);

        // IF HORIZONTAL OR FALLOFF
        if (root.type === TYPES.FALLOFF) {
          newBox.style.backgroundColor = 'gray' // Adjust color as needed
        }
        if (root.nodes.length == 0) { return }

        // IF ROOT
        if (root.type === TYPES.ROOT) {
          this.createBox(root.nodes[0], x, y)
          this.createBox(root.nodes[1], x+root.nodes[0].length*8, y)
          return
        }

        // IF VERTICAL
        let newy = y
        for (let i = 0; i < root.nodes.length; i++) {
          this.createBox(root.nodes[i], x, newy)
          newy += root.nodes[i].width*8
        }
      }
    },
  }
</script>

<style scoped>

</style>