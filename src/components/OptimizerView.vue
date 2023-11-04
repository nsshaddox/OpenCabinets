<template>
  <div v-for="template in templates" :key="template.Name">
    <div v-for="row in template.Components" :key="row">
      <!-- {{ row[1] }} -->
    </div>
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
        root: {},
        numRecurse: 0,
      }
    },
    created () {
      this.partsList = this.createPartsList();
      // this.groupList = this.createGroupList();
      this.optimize()
      console.log("Completed Tree: ", this.root)
      console.log("Number of remaining parts:", this.partsList.length)
      console.log("Parts:", this.partsList)
      
    },
    methods: {
      optimize() {
        this.root = this.createNode(48, 96, TYPES.ROOT, []);
        this.recurse(this.root, this.partsList.length - 1)
      },

      recurse(root, index) {
        console.log("\n====RECURSE CALLED====")
        console.log("numRecurse: ", ++this.numRecurse)
        console.log("root:", root)
        console.log("partsList: ")
        for (let part of this.partsList) {
          console.log(part)
        }

        if (this.numRecurse > 20) { return }
        // console.log("partsList:", this.partsList)
        if (this.partsList.length == 0) { 
          console.log("FALLOFF")
          return 
        }

        //===== HORIZONTAL CUT =====
        if (root.type == TYPES.V_CUT) {
          console.log("Creating Horizontal Cut")
          let length = this.partsList[index][INDEX.LENGTH]
          let width = this.partsList[index][INDEX.WIDTH]
          let newIndex = index
          let remainingWidth = root.width
          let isGood = (length == root.length && width <= remainingWidth)
          
          while (isGood) {
            console.log("Adding: ", this.partsList[newIndex][INDEX.NAME] )
            let partNode = this.createNode(width, length, TYPES.H_CUT, null)
            root.nodes.push(partNode)
            
            remainingWidth -= width
            newIndex--

            if (newIndex < 0) { break }

            width = this.partsList[newIndex][INDEX.WIDTH]
            length = this.partsList[newIndex][INDEX.LENGTH]
            isGood = (length == root.length && width <= remainingWidth)
          }

          // create new list for partsList removing all used parts
          this.partsList = [...this.partsList.slice(0, newIndex + 1), ...this.partsList.splice(index + 1)]
          
          // create new node for the remining ammount, call recurse, and add it to the nodes list
          let rootNode = this.createNode(remainingWidth, root.length, TYPES.ROOT, [])
          
          console.log("rootNode: ", rootNode)
          
          this.recurse(rootNode, this.partsList.length - 1) //remaining part
          root.nodes.push(rootNode)
          
        } 
        
        //===== VERTICAL CUT =====
        else {
          console.log("Creating Vertical Cut")
          console.log(this.root)
          // console.log("length:", INDEX.LENGTH)
          // console.log("index: ", index)
          let length = this.partsList[index][INDEX.LENGTH]
          let width = this.partsList[index][INDEX.WIDTH]
          let i = index
          // find the index of the part that will fit
          while( i > -1 ) {
            length = this.partsList[i][INDEX.LENGTH]
            width = this.partsList[i][INDEX.WIDTH]
            if (length <= root.length && width <= root.width) { break }
            i--
          }
          
          // FALL OFF PIECE!!!
          console.log("i:", i )
          if (i == -1) {
            //root.type = TYPES.FALLOFF
            console.log("FALLOFF")
            return // early return 
          }
          
          let vertNode = this.createNode(root.width, length, TYPES.V_CUT, [])
          let rootNode = this.createNode(root.width, root.length-length, TYPES.ROOT, [])
          
          console.log("vertNode: ", vertNode)
          console.log("rootNode: ", rootNode)
          
          this.recurse(vertNode, i) // matching part
          this.recurse(rootNode, this.partsList.length - 1)// remaining part
          
          root.nodes.push(vertNode)
          root.nodes.push(rootNode)
          
        }
      },
      
      createPartsList() {
        let partsList = []
        console.log(this.templates)
        for (var template of this.templates) {
          for(var part of template.Components) {
            if (part[INDEX.QUAN] > 0 && part[INDEX.MATERIAL] === "Melamine" && part[INDEX.THICK] === 0.75) {
              for (let i = 0; i < part[INDEX.QUAN]; i++ ){
                partsList.push(part);
              }
            }
          }
        }

        partsList.sort((a, b) => {
          // AREA
          //return a[INDEX.WIDTH] * a[INDEX.LENGTH] - b[INDEX.WIDTH] * b[INDEX.LENGTH]

          // LENGTH > WIDTH
          if (a[INDEX.LENGTH] === b[INDEX.LENGTH]) {
            return a[INDEX.WIDTH] - b[INDEX.WIDTH]
          }
          return a[INDEX.LENGTH] - b[INDEX.LENGTH] 
        })

        return partsList
      },

      // GROUPING WORKS!!
      createGroupList() {
        if (this.partsList.length == 0) return []
        
        let remainingWidth = 48
        let groupList = [[]]
        let prevLength = this.partsList[this.partsList.length - 1][INDEX.LENGTH]

        while(this.partsList.length > 0) {
          let part = this.partsList.pop()
          remainingWidth -= part[INDEX.WIDTH]

          if (remainingWidth >= 0 && prevLength == part[INDEX.LENGTH]) {
            groupList[groupList.length - 1].push(part)
          } else {
            //create a new group and add the next part to it
            remainingWidth = 48 - part[INDEX.WIDTH]
            groupList.push([part])
          }

          prevLength = part[INDEX.LENGTH]
          //return groupList
        }

        console.log("groupList:", groupList)

        return groupList
      },

      createNode(width, length, type, nodes){
        var node = {
          "width": width,
          "length": length,
          "type": type,
          "nodes": nodes,
        }
        return node
      }

    },
  }
</script>

<style scoped>

</style>