<script lang="ts">
    import { onMount } from "svelte"
    import { Student, Class, Seat, Room } from "$lib/classes.ts"
    import { browser } from "$app/environment"
    import {exampleClasses} from "$lib/examples.ts"
  
    let mounted = false

    let classes: Class[] = exampleClasses;
  

    onMount(() => {
      if (browser) {
        window.addEventListener("click", resetClickedStatus)
    window.addEventListener("resize", handleResize)

        selectClass(currentClass);

        mounted = true;
      }
  
      return () => {
        if (mounted && browser) {
          window.removeEventListener("click", resetClickedStatus)
          window.removeEventListener("resize", handleResize)
        }
      }
    })
  
  
    function renderRoom() {
      currentRoom.layout = currentRoom.layout.map((row) =>
        row.map((seat) => {
          seat.student = { ...seat.student }
          seat = { ...seat }
          return seat
        }),
      )
    }
  
    let room2 = new Room("Room 2", [
      [
        new Seat(true),
        new Seat(true),
        new Seat(true),
        new Seat(true),
        new Seat(false),
        new Seat(true),
        new Seat(true),
        new Seat(true),
        new Seat(true),
      ],
      [
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
      ],
      [
        new Seat(true),
        new Seat(true),
        new Seat(true),
        new Seat(true),
        new Seat(false),
        new Seat(true),
        new Seat(true),
        new Seat(true),
        new Seat(true),
      ],
      [
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
      ],
      [
        new Seat(true),
        new Seat(true),
        new Seat(true),
        new Seat(true),
        new Seat(false),
        new Seat(true),
        new Seat(true),
        new Seat(true),
        new Seat(true),
      ],
      [
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
      ],
      [
        new Seat(true),
        new Seat(true),
        new Seat(true),
        new Seat(true),
        new Seat(false),
        new Seat(true),
        new Seat(true),
        new Seat(true),
        new Seat(true),
      ],
    ])
  
    let room1 = new Room("Room 1", [
      [
        new Seat(true),
        new Seat(true),
        new Seat(false),
        new Seat(true),
        new Seat(true),
        new Seat(false),
        new Seat(true),
        new Seat(true),
      ],
      [
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
      ],
      [
        new Seat(true),
        new Seat(true),
        new Seat(false),
        new Seat(true),
        new Seat(true),
        new Seat(false),
        new Seat(true),
        new Seat(true),
      ],
      [
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
      ],
      [
        new Seat(true),
        new Seat(true),
        new Seat(false),
        new Seat(true),
        new Seat(true),
        new Seat(false),
        new Seat(true),
        new Seat(true),
      ],
      [
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
      ],
      [
        new Seat(true),
        new Seat(true),
        new Seat(false),
        new Seat(true),
        new Seat(true),
        new Seat(false),
        new Seat(true),
        new Seat(true),
      ],
      [
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
        new Seat(false),
      ],
      [
        new Seat(true),
        new Seat(true),
        new Seat(false),
        new Seat(true),
        new Seat(true),
        new Seat(false),
        new Seat(true),
        new Seat(true),
      ],
    ])
  

    let rooms: Room[] = []
  
    rooms = [room1, room2]

  
    let currentRoom = room1;
    let currentClass = classes[0];

    let allStudents: Student[] = []
    let randomizedStudents: Student[] = []
  
    function shuffleArray(array: any[]) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1))
        ;[array[i], array[j]] = [array[j], array[i]]
      }
      return array
    }
    function selectRoom(room: Room) {
      currentRoom = room
      if (currentClass.name !== "Choose class") {
        selectClass(currentClass)
      }
    }
  
    function selectClass(c: Class) {
      currentClass = c
  
      allStudents = c.students.slice().map((student) => new Student(student))
  
      randomizedStudents = shuffleArray(allStudents.slice())

      createShortNames(c);
      updateRoom()
    }

    function resetClickedStatus() {
      currentRoom.layout.forEach((row) => {
        row.forEach((seat) => {
          seat.student.isClicked = false
        })
      })
  
      clickedStudent1 = undefined
      clickedStudent2 = undefined
      renderRoom()
    }
  

  
    let clickedStudent1: any = undefined
    let clickedStudent2: any = undefined
    let i1: any = 0
    let j1: any = 0
    let i2: any = 0
    let j2: any = 0
  
    function handleClick(
      student: Student,
      event: MouseEvent,
      i: number,
      j: number,
    ) {
      event.stopPropagation()
      student.isClicked = !student.isClicked
      currentRoom.layout[i][j].student = { ...student }
  
      if (student.isClicked) {
        if (clickedStudent1 === undefined && student.name !== "") {
          clickedStudent1 = student
          i1 = i
          j1 = j
        } else if (
          clickedStudent2 === undefined &&
          clickedStudent1 !== undefined
        ) {
          clickedStudent2 = student
          i2 = i
          j2 = j
          moveStudents(clickedStudent1, clickedStudent2)
          resetClickedStatus()
        }
      }
    }
  
    function moveStudents(student1: any, student2: any) {
      let temp = currentRoom.layout[i1][j1].student
      currentRoom.layout[i1][j1].student = currentRoom.layout[i2][j2].student
      currentRoom.layout[i2][j2].student = temp
    }
  
    function updateRoom() {
      let studentList: Student[] = randomizedStudents.slice()
  
      studentList = studentList.filter(student => student.name !== "");

      for (let i = 0; i < currentRoom.layout.length; i++) {
        for (let j = 0; j < currentRoom.layout[i].length; j++) {
          if (currentRoom.layout[i][j].isAvailable && studentList.length > 0) {
            currentRoom.layout[i][j].student = studentList.shift()!
          } else if (currentRoom.layout[i][j].isAvailable) {
            currentRoom.layout[i][j].student = new Student("")
          }
        }
      }
    }
  
    function handlePresense(student: Student) {
      student.isClicked = false
      if (student.isPresent) {
        //remove student from room
        for (let i = 0; i < currentRoom.layout.length; i++) {
          for (let j = 0; j < currentRoom.layout[i].length; j++) {
            if (currentRoom.layout[i][j].student.name === student.name) {
              currentRoom.layout[i][j].student = new Student("")
              allStudents = allStudents.map((s) => {
                if (s.name === student.name) {
                  s.isPresent = false
                }
                return s
              })
            }
          }
        }
  
        //fill the gap of the student
  
        let studentList: any = []
        for (let i = 0; i < currentRoom.layout.length; i++) {
          for (let j = 0; j < currentRoom.layout[i].length; j++) {
            if (
              currentRoom.layout[i][j].isAvailable &&
              currentRoom.layout[i][j].student.name !== ""
            ) {
              studentList.push(currentRoom.layout[i][j].student)
            }
          }
        }
  
        for (let i = 0; i < currentRoom.layout.length; i++) {
          for (let j = 0; j < currentRoom.layout[i].length; j++) {
            if (currentRoom.layout[i][j].isAvailable && studentList.length > 0) {
              currentRoom.layout[i][j].student = studentList.shift()
            } else {
              currentRoom.layout[i][j].student = new Student("")
            }
          }
        }
      } else {
        //add student to room
  
        let found = false
        student.isPresent = true;
        for (let i = 0; i < currentRoom.layout.length; i++) {
          for (let j = 0; j < currentRoom.layout[i].length; j++) {
            if (
              currentRoom.layout[i][j].isAvailable &&
              currentRoom.layout[i][j].student.name === "" &&
              !found
            ) {
              currentRoom.layout[i][j].student = student
              found = true
              break;
            }
          }
        }
      }
    }
  
    //#endregion


    //#region screen width
    let screenWidth = 1440;
    $:if(browser) {
       screenWidth = window.innerWidth;
    }
    
    let smBtnSize = 98;
    let smBtnFactor = 0.12;

    function handleResize() {
      if(browser) {
        screenWidth = window.innerWidth
      }

    }
  
//#endregion
    



function handleRoomSelection(event:any) {
  const selectedRoomName = event.target.value;
  const selectedRoom = rooms.find(room => room.name === selectedRoomName);
  selectRoom(selectedRoom!);
}



function handleClassSelection(event:any) {
  const selectedClassName = event.target.value;
  const selectedClass = classes.find(c=> c.name === selectedClassName);
  selectClass(selectedClass!);
}






//#region student presense
let isDropdownOpen = false;

  function toggleDropdown() {
    isDropdownOpen = !isDropdownOpen;
  }

  function closeDropdown(event:any) {
    const dropdownMenu = document.getElementById("dropdown-menu")!;
    const dropdownButton = document.getElementById("dropdown-button")!;

    if (
      !dropdownMenu.contains(event.target) &&
      !dropdownButton.contains(event.target)
    ) {
      isDropdownOpen = false;
    }
  }






//#endregion




function randomizeClass() {

let presentStudents:Student[] = [];


for(let i = 0; i < currentRoom.layout[0].length; i++){

    for(let j = 0; j < currentRoom.layout.length; j++){


      if(currentRoom.layout[j][i].isAvailable && currentRoom.layout[j][i].student.isPresent){
        presentStudents.push(currentRoom.layout[j][i].student);
      }

    }

  }



randomizedStudents = shuffleArray(presentStudents.slice());
updateRoom();

}





//#region Full names
let showFullNames = false;
let shortNames: {[key: string]: string} = {};
let firstNameCount: {[key: string]: number} = {};

function createShortNames(c: Class) {
      shortNames = {};
      firstNameCount = {};
      
      // Count occurrences of first names
      c.students.forEach(studentName => {
        const firstName = studentName.split(' ')[0];
        firstNameCount[firstName] = (firstNameCount[firstName] || 0) + 1;
      });

      // Create short names based on first name count
      c.students.forEach(studentName => {
        shortNames[studentName] = getShortName(studentName);
      });
    }
    function getShortName(name: string): string {
      const nameParts = name.trim().split(/\s+/);
      
      if (nameParts.length === 1) {
        return name.trim();
      }

      const firstName = nameParts[0];
      const lastNames = nameParts.slice(1);
      
      // If there's more than one student with this first name, add initials
      if (firstNameCount[firstName] > 1) {
        const initials = lastNames.map(lastName => lastName[0].toUpperCase()).join('.');
        return `${firstName} ${initials}`;
      } else {
        return firstName;
      }
    }


//#endregion








  </script>
  
<svelte:window on:click={closeDropdown} />

  <div class="flex justify-center"><button on:click={()=>randomizeClass()} class="btn btn-xs btn-primary mb-3">
    Randomize<svg
      class="w-6 h-6 text-gray-800"
      aria-hidden="true"
      xmlns="http://www.w3.org/2000/svg"
      width="24"
      height="24"
      fill="none"
      viewBox="0 0 24 24"
    >
      <path
        stroke="currentColor"
        stroke-linecap="round"
        stroke-linejoin="round"
        stroke-width="2"
        d="M13.484 9.166 15 7h5m0 0-3-3m3 3-3 3M4 17h4l1.577-2.253M4 7h4l7 10h5m0 0-3 3m3-3-3-3"
      />
    </svg>
  </button>
</div>
  <div class="flex justify-center items-center">

    <select class="select select-sm select-bordered w-52 max-w-xs" on:change={handleRoomSelection}>
      <option disabled>Choose room</option>
      {#each rooms as room, index}
        <option value={room.name} selected={index === 0}>{room.name}</option>
      {/each}
    </select>

    <select class="select select-sm select-bordered w-52 max-w-xs" on:change={handleClassSelection}>
      <option disabled>Choose class</option>
      {#each classes as c, index}
        <option value={c.name} selected={index === 0}>{c.name}</option>
      {/each}
    </select>


    <div class="relative inline-block">
      <button
        id="dropdown-button"
        class="select select-sm w-32 sm:w-52 select-bordered flex items-center justify-start"
        
        on:click={toggleDropdown}
      >
        Students
      </button>
      {#if isDropdownOpen}
      <div
        id="dropdown-menu"
        class="absolute z-10 mt-2 w-full bg-base-200 rounded-md shadow-lg"
      >
        <ul class="py-1 text-sm">
          {#each allStudents as student}
          <li>
            <div class="flex justify-between px-4 py-2">
              {student.name}
              <input
                type="checkbox"
                bind:checked={student.isPresent}
                class="checkbox checkbox-md"
                on:click={() => handlePresense(student)}
              />
            </div>
          </li>
          {/each}
        </ul>
      </div>
      {/if}
    </div>
  </div>
  
  <div class="flex justify-center items-center">
    <div class="mt-2">
      {#each currentRoom.layout as row, i}
        <div class="flex">
          {#each row as box, j}
            {#if box.isAvailable}
              <div class="indicator">
                <div class="indicator-item indicator-top">
                  {#if currentRoom.layout[i][j].student.isClicked && currentRoom.layout[i][j].student.name !== ""}
                    <button
                      on:click={() =>
                        handlePresense(currentRoom.layout[i][j].student)}
                        class="btn btn-xs btn-circle btn-warning">✕</button
                    >
                  {/if}
                </div>
  
                <button
                  on:click={(event) =>
                    handleClick(currentRoom.layout[i][j].student, event, i, j)}
                  class={`btn btn-neutral text-xs btn-sm hover:text-primary hover:border-primary border-2 ${currentRoom.layout[i][j].student.isClicked && currentRoom.layout[i][j].student.name !== "" ? "border-primary text-primary" : ""}`}
                  style={`width: ${screenWidth < 768 ? `${screenWidth * smBtnFactor}px; font-size: 10px;` : `${smBtnSize}px`};`}
                >
              
                <div>
          
                  {#if showFullNames}          
                  {currentRoom.layout[i][j].student.name}
                  
                  {:else}
                  {shortNames[currentRoom.layout[i][j].student.name] || currentRoom.layout[i][j].student.name}
                  {/if}
                </div>
                </button>
              </div>
            {:else}
              <div class="btn btn-disabled btn-sm" style={`width: ${screenWidth < 768 ? `${screenWidth * smBtnFactor}px` : `${smBtnSize}px`};`}></div>
            {/if}
          {/each}
        </div>
      {/each}
    </div>
  </div>
  

<div class="flex justify-center mt-5">

  <div class="form-control flex gap-2">
    <label class="label cursor-pointer">
      <span class="label-text">Show full names</span>
      <input type="checkbox" bind:checked={showFullNames} class="checkbox mx-2" />
    </label>
  </div>

</div>