
# Assignment 2: Functional Design

## Domain: Mastering Material in School
The domain focuses on how students absorb and retain course material. As a student at MIT, I want to maximize my learning and performance. I want focus on not just getting good grades but also truly understanding the material.  Currently, I struggle with taking effective notes and keeping up with lectures, and I have many friends do too.  In fact, because of how hard it is to keep up in some lectures, many students don't even go to lecture because it's not worth their time. Improving note-taking and study strategies is crucial for academic performance and long-term understanding. 

## Problem: Taking Notes in Fast Lectures
Students often miss key points when lectures move quickly and deliver information both verbally and visually. This leads to incomplete notes, extra review time, stress, and lower academic performance.

## Stakeholders
- **Student (direct user):** Poorer notes lead to more stress, more time to review for tests, and less comprehension and retention. This could also lead to lower test scores, and some students even skipping lecture because they don't think it's useful. 
- **Teacher/Lecturer (non-user):** Students might appear disengaged if they're struggling to take notes and might appear in office hours more often.
- **Parents/Guardians (non-user):** They might worry about academic performance and study habits of their student, and might feel stressed as well.

## Evidence & Comparables
 1. [You're only recording 60% of the important points in a lecture on average.](https://crlt.umich.edu/sites/default/files/resource_files/CRLT_no16.pdf) Research indicates that students fail to record 40% of the important points in a typical lecture.
   2. [First-year students record less, only 11% of the important points in a lecture](https://crlt.umich.edu/sites/default/files/resource_files/CRLT_no16.pdf)First-year students record, on average, only 11% of the important points in a typical lecture(Locke, 1977).
   3. [Only 10% of oral information is recorded, as more focus is put on slides/blackboards.](https://crlt.umich.edu/sites/default/files/resource_files/CRLT_no16.pdf) Students record most of the blackboard information, but only about 10% of information delivered orally.
   4. [Some listeners perform better when the material was presented at a normal rate, or when note-taking was not required suggesting students need time to process and absorb the material.](https://psycnet.apa.org/record/1972-27861-001) Research indicates indicates that that a moderate moderate speed speed of delivery, around 135 words per minute, best supports student notetaking.
   5. [There's a correlation between recording better notes (with more information and better structure) and performing better in class](https://www.sciencedirect.com/science/article/abs/pii/S1041608014001198?utm_source=chatgpt.com) Researchers reported that successful students recorded more notes than less successful college students. Though these notes only comprised of 25%-33% of the ideas of the lecture, these notes also had better organization and structure of the lecture than less successful students had.
   6. [Collaborative peer note-takers perform better than individual note takers on recall tasks, but not writing tasks](https://www.sciencedirect.com/science/article/pii/S109675162200029X) A study on collaborative note taking suggests "that access to high-quality notes are a driver of learning performance on quizzes, which explains the superior results of collaborative note-takers in this regard, whereas the process of recording notes may drive a learner's ability to apply these concepts in the form of academic writing, which explains the superior results of the individual note-takers in this regard"

**Comparable Applications:**
   1. [Comparable 1:Groupon](https://www.groupon.com/coupons/categories/department-stores?srsltid=AfmBOoqLXyt54m0W_f3D3uKfNfaA5yXM2heQZdB9OPxvaWC0EDvxxNU_) Groupon helps wit finding coupons, but not with finding clothing-specific searches.
   2. [Comparable 2: Google Docs](https://docs.google.com/) Google docs is a free platform to take notes. Many students like typing there because it is faster and allows for real-time collaboration. However, there isn't support for hand-written notes and according to research, hand-written notes is better for retention than typed notes are.
   3. [Comparable 3: GoodNotes](https://www.goodnotes.com/) GoodNotes is known primarily for its handwriting experience. You can write notes by hand and search it up later as well. It's also useful to sync across platforms. However, it's known to be very buggy and expensive.
---

# Application Pitch

**Name:** Scriblink 
- (Scribble + Ink or Scrible + Link)  

This application helps students actively learn, remember, and understand lecture material.  

**Key Features:**
1. **Shared Handwritten Notetaking:** The main feature will be real-time collaborative handwritten notes, including diagrams and annotations. This saves time and improves comprehension because students can be present in the lecture.  Additionally, the student can bounce ideas off the person that they are taking notes with.  It's better than Google Docs, which is what we used to use in high school, because you can use it for technical classes (ie math equations) as well with the handwriting feature.  This will help improve students' comprehension of lecture materials, benefiting all stakeholers because the student gets better academic performance.
2. **Automated Tagging & Summarizing:** Notes are also automatically summarized and tagged by topic, easing review and identifying confusing sections.  This will be done by an AI agent after lecture is over.  When the student comes back to review at a future date, it will be easy to find the places that they struggled and will get additional insight by the summaries provided by the AI agent.
3. **Potential Future Feature - AI Tutor:** If you have trouble with one of the sections, you can ask the AI to help you understand the concept.  The AI will use your notes as context for the chat.
4. **Potential Future Feature – Study Integration:** There can also be flashcards and custom review questions generated from lecture notes to optimize study sessions.  Students can also add their own questions and the AI can generate varients of that (such as questions from past exams).  These flashcard sets will remain private to the user so that copyright material is not distributed to a bunch of people (to address the concerns of stakeholders like the teacher an university).

---

# Concept Design

## Concepts

**CollaborativeNotes[User]**
- **Purpose** Record information

- **State** Set of Notes with
    - date_created Date
    - set of collaboraters User
    - a `share_code` String

**UserNaming**  
- **Purpose:** Name users  
- **Principle:** After registering with a name, the user can be recognized by other users that they are collaborating with.
- **State:** Set of Users with 
    - a `username` string  
    - a `name` string

**PasswordAuth[User]**  
- **Purpose:** Authenticate users  
- **Principle:** After setting a password for a user, the user can authenticate with that password
- **State:** Set of Users with 
    - `password` string  
- **Actions:**
    - setPassword(u: User, p: String)
    - authenticate(u: User, p: String)

**Sections[Notes]**  
- **Purpose** Split the notes into seperate topics
- **Principle:** 
- **State:** set of Section with 
    - `note` Note
    - `position` number


**Tags[Section]** 
- **State:** Set of tags with 
    - `label` string
    - set of `section` 

**Summaries[Section]**
- **State:** Set of summaries with 
    - `text` string 
    - set of `section` 


**Folder[Item]**  
- **Purpose:** Organize items hierarchically  
- **Principle:** Items remain with the folder even if moved  
- **State:** Set of Folders with `name`, contained Folders, and `elements` (Items)  
- **Actions:** `insert(i: Item, f: Folder)`  

## Synchronization Notes
**sync** CreateAccount
**when**
- Request.createAccount (name, password)
**then**
- UserNaming.register (name)
- PasswordAuth.setPassword (user, password)




**Role in App:** 
First, there is the Notes concept.  One note is a file, meant to be used for a single lecture.  Each note is associated with a couple of users that contributed to taking notes in the class, as well as a code to share to people to join the notes.  Sections are divisions within a single note.  This is to help with a few things, but the main part is to organize the notes into topics.  In a sync, we'll also attach tags and summaries to seperate sections.  Additionally, the sections also help the UI.  In order to make sure that people aren't writing on top of each other, you can create new sections that can be written on seperately.  For instance, if I'm writing about how to write a concept from a 6.104 lecture, and the professor suddenly moves to AI, my friend could start on the AI by making a new section.  It will allow me to continue expanding downwards without colliding with my friend. Once I write down my last notes, I can join my friend's section and annotate and write with them.  Folders are to help organize notes, perhaps into classes or whatever the user desires.  User keeps trakcs of users, and the Authenticate concept authenticates users. Only certain users can access certain notes, so the authentication concept is important. Finally, tags help with searching and quickly understanding what the notes are about without going through it.  The summaries add additional insight to sections from the AI to help with understanding and are also connected to the sections that users create themselves.


## UI Sketches
![Scriblink UI Sketches](../assets/UISketch.PNG)
(Final AI chat screen is a stretch goal if I can finish the first two.)

## User journey
During calculus class, a student isn't engaged because they are more focused on taking notes than being present in the class and actually digesting what they are hearing.  The student has to rewatch lecture videos consistantly, and falls further and further behind.  They need a better way to actually absorb the material and save time.  They open up the note taking app.  They create a new note, and invite a friend to join the session.  Now in lecture, they work together to create notes.  They have more time to actually listen to what the lecturer is saying, can bounce their ideas off each other.  The split up their notes into seperate sections to make it more managemable.

When the lecture is over, the AI automatically tags the notes an summarizes the sections.  When it's midterm season, the students come back to the notes to find their notes.  The student is having trouble remembering what the u-subsitution is.  The AI has a summary and additional insights about u-substitution that the student didn't note down.


Potential Features (Stretch goal):

The student feels like they're kind of remembering what U-substitution is, but needs a little more help.  They click on the AI tutor button.  The AI tutor already has context from the student's notes, and can start helping the student right away.  The student asks questions and some practice problems that the AI walks them through.  The student comes out with a better understanding of all the lecture material and is better prepared for the midterm.

To be better prepared for the exam itself, the student inputs their own questions into the flashcards and practice question section.  They also upload questions from previous exams.  The student has an easy way to track progress and practice on the app now, as well as generate vareints of questions they want more practice with and that look like exam questions.  After practicing with this tool, the student is much more well prepared for the exam and in general understands the calculus content a lot more deeply.




