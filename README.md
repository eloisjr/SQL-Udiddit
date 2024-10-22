# SQL-Udiddit

Udiddit is a social news aggregator, content rating, and discussion website. On Udiddit, registered users can link to external content or post their text about various topics, ranging from common topics such as photography and food to more arcane ones such as horse masks or birds with arms. In turn, other users can comment on these posts, and each user can vote about each post, either in an up (like) or down (dislike) direction.

# Objective 

**Part I: Schema Investigation - 3 Areas for Improvement**

- **Normalization Issues**: Some tables may not be normalized, leading to data redundancy and potential update anomalies. For instance, user information and posts could be combined into one table, which is inefficient.
- **Lack of Constraints**: There are likely missing constraints such as unique constraints on usernames or topic names, and not null constraints for required fields. This allows invalid data entry, affecting data integrity.
- **Threaded Comments Structure**: The existing schema might only support linear comments, which is a limitation for complex discussions. Implementing a structure that allows threaded comments at arbitrary levels is necessary.

**Part II: DDL for New Schema**

- The users table enforces unique usernames with a maximum length of 25 characters and ensures usernames are not empty.
  
- The topics table allows users to create unique topic names, with a character limit of 30, and includes an optional description field with a maximum of 500 characters.
  
- The posts table requires each post to have a title of up to 100 characters, allows either a URL or text content but not both, and includes references to the associated user and topic.

- Posts are deleted when their topic is removed, but they remain even if the user who created them is deleted.
  
- The comments table supports threaded comments by allowing each comment to reference a parent comment. When a comment is deleted, all its descendant comments are also removed.

- The votes table stores upvotes and downvotes as 1 and -1, ensuring users can only vote once on a post. Votes are retained if the user is deleted but are removed when the post is deleted.

**Part III: Data Migration Steps**

- Migrate topics: Insert all topics from the old schema into the new one, ensuring that topic descriptions are empty if they weren’t present in the old data.
- Migrate users: Create unique users by selecting distinct usernames from the old schema and inserting them into the new schema.
- Migrate posts: Insert posts by associating them with the correct users and topics from the new schema, ensuring that either the URL or content is present.
- Migrate comments: Insert comments, treating them all as top-level comments for now, since the old schema lacks threading.
- Migrate votes: Use PostgreSQL’s regexp_split_to_table function to split comma-separated votes and insert them into the new votes table, linking them to users and posts.

  
These steps ensure the migration of the old data to the new schema while maintaining consistency and avoiding data loss.
