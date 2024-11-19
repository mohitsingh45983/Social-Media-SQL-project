CREATE DATABASE SocialMedia;
USE SocialMedia; 

CREATE TABLE User (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(100) NOT NULL UNIQUE,
    date_of_birth DATE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO User (username, email, date_of_birth)
VALUES
('john_doe', 'john.doe@example.com', '1990-05-15'),
('jane_smith', 'jane.smith@example.com', '1988-07-20'),
('alice_wonder', 'alice.wonder@example.com', '1995-03-12'),
('bob_builder', 'bob.builder@example.com', '1992-10-05'),
('charlie_brown', 'charlie.brown@example.com', '2000-01-25');


CREATE TABLE Post (
    post_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    content TEXT NOT NULL,
    post_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    privacy VARCHAR(10) CHECK (privacy IN ('public', 'private')),
    comment_count INT DEFAULT 0,
    FOREIGN KEY (user_id) REFERENCES User(user_id) ON DELETE CASCADE
);

INSERT INTO Post (user_id, content, privacy, comment_count)
VALUES
(1, 'Excited to start my new job!', 'public', 2),
(2, 'Loving the sunny weather today!', 'public', 1),
(3, 'Just finished a great book. Highly recommend it!', 'private', 0),
(4, 'Does anyone have recommendations for good movies?', 'public', 3),
(5, 'Had a fantastic day hiking in the mountains.', 'private', 1);


CREATE TABLE Comment (
    comment_id INT PRIMARY KEY AUTO_INCREMENT,
    post_id INT NOT NULL,
    user_id INT NOT NULL,
    content TEXT NOT NULL,
    comment_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (post_id) REFERENCES Post(post_id) ON DELETE CASCADE,
    FOREIGN KEY (user_id) REFERENCES User(user_id) ON DELETE CASCADE
);

INSERT INTO Comment (post_id, user_id, content)
VALUES
(1, 2, 'Congratulations on the new job!'),
(1, 3, 'Wishing you the best!'),
(2, 4, 'Yes, sunny days are the best!'),
(4, 1, 'I recommend watching "Inception." It\'s amazing!'),
(4, 5, 'You should check out "Interstellar." It\'s a great movie.'),
(4, 3, 'I loved "The Dark Knight" trilogy.'),
(5, 2, 'Hiking is so refreshing! Glad you enjoyed it.');


CREATE TABLE Friendship (
    friendship_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id_1 INT NOT NULL,
    user_id_2 INT NOT NULL,
    friendship_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id_1) REFERENCES User(user_id) ON DELETE CASCADE,
    FOREIGN KEY (user_id_2) REFERENCES User(user_id) ON DELETE CASCADE,
    CHECK (user_id_1 <> user_id_2)  -- Prevents a user from being friends with themselves
);

CREATE TABLE Reaction (
    reaction_id INT PRIMARY KEY AUTO_INCREMENT,
    post_id INT NOT NULL,
    user_id INT NOT NULL,
    reaction_type VARCHAR(10) CHECK (reaction_type IN ('like', 'love', 'haha', 'wow', 'sad', 'angry')),
    reaction_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (post_id) REFERENCES Post(post_id) ON DELETE CASCADE,
    FOREIGN KEY (user_id) REFERENCES User(user_id) ON DELETE CASCADE
);

 
 CREATE VIEW UserPosts AS
SELECT u.user_id, u.username, p.post_id, p.content, p.post_date
FROM User u
JOIN Post p ON u.user_id = p.user_id;

 CREATE VIEW PostReactions AS
SELECT p.post_id, p.content, r.reaction_type, COUNT(r.reaction_id) AS reaction_count
FROM Post p
JOIN Reaction r ON p.post_id = r.post_id
GROUP BY p.post_id, r.reaction_type;

DELIMITER //

CREATE PROCEDURE AddFriend(IN user1 INT, IN user2 INT)
BEGIN
    -- Check if user1 is trying to add themselves as a friend
    IF user1 = user2 THEN
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'A user cannot add themselves as a friend';
    ELSE
        -- Check if the friendship already exists (either direction)
        IF NOT EXISTS (
            SELECT 1 FROM Friendship 
            WHERE (user_id_1 = user1 AND user_id_2 = user2) 
               OR (user_id_1 = user2 AND user_id_2 = user1)
        ) THEN
            -- Add the friendship
            INSERT INTO Friendship (user_id_1, user_id_2, friendship_date)
            VALUES (user1, user2, NOW());
        ELSE
            SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Friendship already exists';
        END IF;
    END IF;
END //

DELIMITER ;



DELIMITER //

CREATE PROCEDURE AddReaction(IN post INT, IN user INT, IN reactionType VARCHAR(10))
BEGIN
    INSERT INTO Reaction (post_id, user_id, reaction_type)
    VALUES (post, user, reactionType);
END //

DELIMITER ;


DELIMITER //

CREATE TRIGGER UpdateCommentCount
AFTER INSERT ON Comment
FOR EACH ROW
BEGIN
    UPDATE Post
    SET comment_count = comment_count + 1
    WHERE post_id = NEW.post_id;
END //

DELIMITER ;

DELIMITER //

CREATE TRIGGER LogReactionTimestamp
BEFORE INSERT ON Reaction
FOR EACH ROW
BEGIN
    SET NEW.reaction_date = NOW();
END //

DELIMITER ;

SELECT * FROM user;

SELECT * FROM Comment;

SELECT * FROM Friendship;

-- using procedures
CALL AddFriend(1,6); 
CALL AddReaction(1, 2, 'like');  
CALL AddReaction(9, 1, 'wow');  
CALL AddReaction(9, 3, 'wow'); 

-- using views 
SELECT * FROM userPosts;
SELECT * FROM PostReactions;




