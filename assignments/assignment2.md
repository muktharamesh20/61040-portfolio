
# Assignment 2: Functional Design

## Domain: Mastering Material in School
The domain focuses on how students absorb and retain course material. As a student at MIT, I want to maximize my learning and performance. I want focus on not just getting good grades but also truly understanding the material.  Currently, I struggle with taking effective notes and keeping up with lectures, and I have many friends do too.  In fact, because of how hard it is to keep up in some lectures, many students don't even go to lecture because it's not worth their time. Improving note-taking and study strategies is crucial for academic performance and long-term understanding. 

## Problem: Taking Notes in Fast Lectures
Students often miss key points when lectures move quickly and deliver information both verbally and visually. This leads to incomplete notes, extra review time, stress, and lower academic performance.

## Stakeholders
- **Student (direct user):** The student is directly affected. Poorer notes lead to more stress, more time to review for tests, and less comprehension and retention. This could also lead to lower test scores, and some students even skipping lecture because they don't think it's useful. 
- **Teacher/Lecturer (non-user):** Students might appear disengaged if they're struggling to take notes. They also might appear in office hours more often, and need more help.
- **Parents/Guardians (non-user):** They might worry about academic performance and study habits of their student, and might feel stressed as well. A solution that helps their kid study would make them feel better.

## Evidence & Comparables
 1. [You're only recording 60% of the important points in a lecture on average.](https://crlt.umich.edu/sites/default/files/resource_files/CRLT_no16.pdf) Research indicates that students fail to record 40% of the important points in a typical lecture.
   2. [First-year students record less, only 11% of the important points in a lecture](https://crlt.umich.edu/sites/default/files/resource_files/CRLT_no16.pdf)First-year students record, on average, only 11% of the important points in a typical lecture(Locke, 1977).
   3. [Only 10% of oral information is recorded, as more focus is put on slides/blackboards.](https://crlt.umich.edu/sites/default/files/resource_files/CRLT_no16.pdf) Students record most of the blackboard information, but only about 10% of information delivered orally.
   4. [Some listeners perform better when the material was presented at a normal rate, or when note-taking was not required suggesting students need time to process and absorb the material.](https://psycnet.apa.org/record/1972-27861-001) Research indicates indicates that that a moderate moderate speed speed of delivery, around 135 words per minute, best supports student notetaking.
   5. [There's a correlation between recording better notes (with more information and better structure) and performing better in class](https://www.sciencedirect.com/science/article/abs/pii/S1041608014001198?utm_source=chatgpt.com) Researchers reported that successful students recorded more notes than less successful college students. Though these notes only comprised of 25%-33% of the ideas of the lecture, these notes also had better organization and structure of the lecture than less successful students had.
   6. [Note-taking tools improve speed but have the potential to increase or reduce cognitive engagement.](https://arxiv.org/pdf/2509.03392) A study found out of three levels of AI engagement (high assistance with structured notes, moderate asistance with real-time summary, and low assitance with transcript), the moderate use of AI resulted in the highest post-test scores, with the high use of AI resulting in the lowest. Interestingly, people preferred the high assitance due to the ease of use and effort even though they scored the lowest.
   7. [Collaborative peer note-takers perform better than individual note takers on recall tasks, but not writing tasks](https://www.sciencedirect.com/science/article/pii/S109675162200029X) A study on collaborative note taking suggests "that access to high-quality notes are a driver of learning performance on quizzes, which explains the superior results of collaborative note-takers in this regard, whereas the process of recording notes may drive a learner's ability to apply these concepts in the form of academic writing, which explains the superior results of the individual note-takers in this regard"

**Comparable Applications:**
   1. [Comparable 1: MaRginalia](https://dl.acm.org/doi/10.1145/3706598.3714065) MaRginalia is a note-taking system which automatically takes screenshots of lecture slids and creates a transcript from the lecturer's words. The user can then take notes on these slides screenshots and the transcript. According to users, it was beneficial to catch up on missed information, helpful not to rewrite what the lecturer was saying, and to stay focussed on the slides and professor, while others said it was overcomplex. One additional con is that it requires specialized equipment as it's a tablet and a VR headset.
   2. [Comparable 2: Google Docs](https://docs.google.com/) Google docs is a free platform to take notes. Many students like typing there because it is faster and allows for real-time collaboration. However, there isn't support for hand-written notes and according to research, hand-written notes is better for retention than typed notes are.
   3. [Comparable 3: GoodNotes](https://www.goodnotes.com/) GoodNotes is known primarily for its handwriting experience. You can write notes by hand and search it up later as well. It's also useful to sync across platforms. However, it's known to be very buggy and expensive.
---

# Application Pitch

**Name:** Scriblink 
- (Scribble + Ink or Scrible + Link)  

**Motivation:** This application helps students actively learn, remember, and understand lecture material.  

**Key Features:**
1. **Shared Handwritten Notetaking:** The main feature will be real-time collaborative handwritten notes, including diagrams and annotations. This saves time and improves comprehension because students can be present in the lecture.  Additionally, the student can bounce ideas off the person that they are taking notes with.  It's better than Google Docs, which is what we used to use in high school, because you can use it for technical classes as well with the handwriting feature.  This will help improve students' comprehension of lecture materials, benefiting all parties because the student gets better academic performance.
2. **Automated Tagging & Summarizing:** Notes are also automatically summarized and tagged by topic, easing review and identifying confusing sections.  This will be done by an AI agent after lecture is over.  When the student comes back to review at a future date, it will be easy to find the places that they struggled and will get additional insight by the summaries provided by the AI agent.
3. **Potential Future Feature - AI Tutor:** If you have trouble with one of the sections, you can ask the AI to help you understand the concept.  The AI will use your notes as context for the chat.
4. **Potential Future Feature – Study Integration:** If there's time, perhaps there can also be flashcards and custom review questions generated from lecture notes to optimize study sessions.  Students can also add their own questions and the AI can generate varients of that (such as questions from past exams).  These flashcard sets will remain private to the user so that copyright material is not distributed to a bunch of people.  This addresses the concerns of the teacher.

---

# Concept Design

## Concepts

**UserNaming**  
- **Purpose:** Name users  
- **State:** Set of Users with a `username` string  

**PasswordAuth [User]**  
- **Purpose:** Authenticate users  
- **State:** Set of Users with `password` string  

**Tags**  
- **State:** Set of tags with `label` and `section`  

**Summaries**
- **State:** Set of summaries with `label` and `section` 

**Sections**  
- **State:** Set of handwritten notes  

**Folder [Item]**  
- **Purpose:** Organize items hierarchically  
- **Principle:** Items remain with the folder even if moved  
- **State:** Set of Folders with `name`, contained Folders, and `elements` (Items)  
- **Actions:** `insert(i: Item, f: Folder)`  

## Synchronization Notes
- Multi-user note collaboration requires syncing `Sections` across `Users`.  
- Tagging and summarizing need updates across all relevant `Sections`.  
- Authentication controls access to folders, notes, and tags.

**Role in App:** 
First, there is the Notes concept.  One note is a file, meant to be used for a single lecture.  Each note is associated with a couple of users that contributed to taking notes in the class, as well as a code to share to people to join the notes.  Sections are divisions within a single note.  This is to help with a few things, but the main part is to organize the notes into topics.  In a sync, we'll also attach tags and summaries to seperate sections.  Additionally, the sections also help the UI.  In order to make sure that people aren't writing on top of each other, you can create new sections that can be written on seperately.  For instance, if I'm writing about how to write a concept from a 6.104 lecture, and the professor suddenly moves to AI, my friend could start on the AI by making a new section.  It will allow me to continue expanding downwards without colliding with my friend. Once I write down my last notes, I can join my friend's section and annotate and write with them.  Folders are to help organize notes, perhaps into classes or whatever the user desires.  User keeps trakcs of users, and the Authenticate concept authenticates users. Only certain users can access certain notes, so the authentication concept is important. Finally, tags help with searching and quickly understanding what the notes are about without going through it.  The summaries add additional insight to sections from the AI to help with understanding and are also connected to the sections that users create themselves.


## UI Sketches
![Scriblink UI Sketches](../assets/UISketch.PNG)

## User journey
During calculus class, a student isn't engaged because they are more focused on taking notes than being present in the class and actually digesting what they are hearing.  The student has to rewatch lecture videos consistantly, and falls further and further behind.  They need a better way to actually absorb the material and save time.  They open up the note taking app.  They create a new note, and invite a friend to join the session.  Now in lecture, they work together to create notes.  They have more time to actually listen to what the lecturer is saying, can bounce their ideas off each other.  The split up their notes into seperate sections to make it more managemable.

When the lecture is over, the AI automatically tags the notes an summarizes the sections.  When it's midterm season, the students come back to the notes to find their notes.  The student is having trouble remembering what the u-subsitution is.  The AI has a summary and additional insights about u-substitution that the student didn't note down.  The student feels like they're kind of remembering what U-substitution is, but needs a little more help.  They click on the AI tutor button.  The AI tutor already has context from the student's notes, and can start helping the student right away.  The student asks questions and some practice problems that the AI walks them through.  The student comes out with a better understanding of all the lecture material and is better prepared for the midterm.




