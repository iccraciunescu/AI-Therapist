# AI-Therapist
# AI Therapy Platform 

This file contains all the code from the AI Therapy Platform project for export purposes.

## Table of Contents
1. [Configuration Files](#configuration-files)
2. [Server Files](#server-files)
3. [Shared Files](#shared-files)
4. [Client Files](#client-files)

## Configuration Files

### package.json
```json
{
  "name": "rest-express",
  "version": "1.0.0",
  "license": "MIT",
  "scripts": {
    "dev": "tsx server/index.ts",
    "build": "vite build",
    "start": "node dist/index.js",
    "db:push": "drizzle-kit push:pg",
    "db:studio": "drizzle-kit studio"
  }
}
```

### drizzle.config.ts
```typescript
import type { Config } from "drizzle-kit";

export default {
  schema: "./shared/schema.ts",
  out: "./drizzle",
  driver: "pg",
  dbCredentials: {
    connectionString: process.env.DATABASE_URL || "postgresql://postgres:postgres@localhost:5432/postgres",
  },
} satisfies Config;
```

### vite.config.ts
```typescript
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import path from "path";
import { readdirSync, statSync } from "fs";
import Runtime from "@replit/vite-plugin-runtime-error-modal";
import Cartographer from "@replit/vite-plugin-cartographer";
import shadcn from "@replit/vite-plugin-shadcn-theme-json";

// List all directories under /components/ui
let shadcnComponents: string[] = [];
const uiDir = path.resolve("./client/src/components/ui");
try {
  shadcnComponents = readdirSync(uiDir)
    .filter((file) => {
      try {
        return statSync(path.join(uiDir, file)).isFile() && file.endsWith(".tsx");
      } catch (e) {
        return false;
      }
    })
    .map((file) => file.replace(/\.tsx$/, ""));
} catch (e) {
  console.log("No shadcn components found.");
}

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    Runtime(),
    react(),
    Cartographer({
      excludeDefaults: false,
      entries: [
        {
          path: "./client/src/main.tsx",
          include: ["**/*.tsx", "**/*.ts", "**/*.jsx", "**/*.js"],
          exclude: ["node_modules/**/*", "**/node_modules/**"],
        },
      ],
    }),
    shadcn(),
  ],
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./client/src"),
      "@shared": path.resolve(__dirname, "./shared"),
      "@server": path.resolve(__dirname, "./server"),
      "@client": path.resolve(__dirname, "./client"),
      "@components": path.resolve(__dirname, "./client/src/components"),
      "@hooks": path.resolve(__dirname, "./client/src/hooks"),
      "@lib": path.resolve(__dirname, "./client/src/lib"),
      "@ui": path.resolve(__dirname, "./client/src/components/ui"),
    },
  },
  build: {
    emptyOutDir: true,
    outDir: 'dist',
  },
});
```

### tailwind.config.ts
```typescript
/** @type {import('tailwindcss').Config} */
module.exports = {
  darkMode: ["class"],
  content: [
    './client/src/**/*.{js,jsx,ts,tsx}',
  ],
  theme: {
    container: {
      center: true,
      padding: "2rem",
      screens: {
        "2xl": "1400px",
      },
    },
    extend: {
      colors: {
        border: "hsl(var(--border))",
        input: "hsl(var(--input))",
        ring: "hsl(var(--ring))",
        background: "hsl(var(--background))",
        foreground: "hsl(var(--foreground))",
        primary: {
          DEFAULT: "hsl(var(--primary))",
          foreground: "hsl(var(--primary-foreground))",
        },
        secondary: {
          DEFAULT: "hsl(var(--secondary))",
          foreground: "hsl(var(--secondary-foreground))",
        },
        destructive: {
          DEFAULT: "hsl(var(--destructive))",
          foreground: "hsl(var(--destructive-foreground))",
        },
        muted: {
          DEFAULT: "hsl(var(--muted))",
          foreground: "hsl(var(--muted-foreground))",
        },
        accent: {
          DEFAULT: "hsl(var(--accent))",
          foreground: "hsl(var(--accent-foreground))",
        },
        popover: {
          DEFAULT: "hsl(var(--popover))",
          foreground: "hsl(var(--popover-foreground))",
        },
        card: {
          DEFAULT: "hsl(var(--card))",
          foreground: "hsl(var(--card-foreground))",
        },
      },
      borderRadius: {
        lg: "var(--radius)",
        md: "calc(var(--radius) - 2px)",
        sm: "calc(var(--radius) - 4px)",
      },
      keyframes: {
        "accordion-down": {
          from: { height: 0 },
          to: { height: "var(--radix-accordion-content-height)" },
        },
        "accordion-up": {
          from: { height: "var(--radix-accordion-content-height)" },
          to: { height: 0 },
        },
      },
      animation: {
        "accordion-down": "accordion-down 0.2s ease-out",
        "accordion-up": "accordion-up 0.2s ease-out",
      },
    },
  },
  plugins: [require("tailwindcss-animate"), require("@tailwindcss/typography")],
}
```

### theme.json
```json
{
  "variant": "vibrant",
  "primary": "hsl(250, 84%, 54%)",
  "appearance": "light",
  "radius": 0.6
}
```

### postcss.config.js
```javascript
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}
```

### tsconfig.json
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "skipLibCheck": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,

    /* Bundler mode */
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",

    /* Linting */
    "strict": true,
    "noUnusedLocals": false,
    "noUnusedParameters": false,
    "noFallthroughCasesInSwitch": true,
    "noImplicitAny": false,
    
    "paths": {
      "@/*": ["./client/src/*"],
      "@components/*": ["./client/src/components/*"],
      "@lib/*": ["./client/src/lib/*"],
      "@hooks/*": ["./client/src/hooks/*"],
      "@shared/*": ["./shared/*"],
      "@server/*": ["./server/*"],
      "@client/*": ["./client/*"],
      "@ui/*": ["./client/src/components/ui/*"]
    }
  },
  "include": ["client/src", "server", "shared"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

## Server Files

### server/index.ts
```typescript
import express, { Request, Response, NextFunction } from "express";
import * as vite from "./vite";
import { registerRoutes } from "./routes";
import { createServer } from "http";
import session from "express-session";
import passport from "passport";
import { Strategy as LocalStrategy } from "passport-local";
import MemoryStore from "memorystore";
import { pool } from "./db";

// Postgres session store
const pgSessionStore = require("connect-pg-simple")(session);
const MemorySessionStore = MemoryStore(session);

// Import user-related functions
import { storage } from "./storage";

async function main() {
  // Create and configure Express application
  const app = express();
  const httpServer = createServer(app);

  // Session configuration
  const sessionOptions: session.SessionOptions = {
    secret: process.env.SESSION_SECRET || "therapy-app-secret",
    resave: false,
    saveUninitialized: false,
    cookie: {
      secure: process.env.NODE_ENV === "production",
      maxAge: 24 * 60 * 60 * 1000, // 24 hours
    },
    store: process.env.DATABASE_URL ? 
      new pgSessionStore({
        pool: pool,
        tableName: "session",
        createTableIfMissing: true,
      }) :
      new MemorySessionStore({
        checkPeriod: 86400000 // prune expired entries every 24h
      }),
  };

  // Apply middleware
  app.use(express.json());
  app.use(session(sessionOptions));
  app.use(passport.initialize());
  app.use(passport.session());

  // Passport configuration
  passport.use(
    new LocalStrategy(async (username, password, done) => {
      try {
        const user = await storage.getUserByUsername(username);
        
        if (!user) {
          return done(null, false, { message: "Incorrect username" });
        }
        
        // In a real app, we would verify the password hash here
        // For demo purposes, we're accepting any password
        return done(null, user);
      } catch (err) {
        return done(err);
      }
    })
  );

  passport.serializeUser((user: any, done) => {
    done(null, user.id);
  });

  passport.deserializeUser(async (id: number, done) => {
    try {
      const user = await storage.getUser(id);
      done(null, user);
    } catch (err) {
      done(err);
    }
  });

  // Register API routes
  const server = await registerRoutes(app);

  // Vite setup
  await vite.setupVite(app, server);

  // Error handler
  app.use((err: any, _req: Request, res: Response, _next: NextFunction) => {
    console.error(err.stack);
    res.status(500).json({ error: err.message });
  });

  // Start server
  const PORT = process.env.PORT || 5000;
  httpServer.listen(PORT, "0.0.0.0", () => {
    vite.log(`serving on port ${PORT}`);
  });
}

main().catch((err) => {
  console.error(err);
  process.exit(1);
});
```

### server/routes.ts
```typescript
import { Express, Request, Response } from "express";
import { Server } from "http";
import { storage } from "./storage";
import { WebSocketServer, WebSocket } from 'ws';
import { getTherapistResponse, TherapyMessage } from "./lib/openai";
import { textToSpeech } from "./lib/elevenlabs";
import { createInsertSchema } from "drizzle-zod";
import { z } from "zod";
import { insertUserSchema, insertTherapySessionSchema, insertMessageSchema, insertMoodCheckSchema } from "@shared/schema";

// Active therapy sessions
interface ActiveSession {
  userId: number;
  sessionId: number;
  socket: WebSocket;
  messages: TherapyMessage[];
}

const activeSessions: Map<number, ActiveSession> = new Map();

export async function registerRoutes(app: Express): Promise<Server> {
  const httpServer = app.listen();

  // WebSocket setup
  const wss = new WebSocketServer({ server: httpServer, path: '/ws' });

  wss.on('connection', (socket: WebSocket) => {
    console.log('WebSocket client connected');
    let activeSession: ActiveSession | null = null;

    socket.on('message', async (message) => {
      try {
        const data = JSON.parse(message.toString());
        
        if (data.type === 'join') {
          // Initialize or join a therapy session
          const userId = data.userId;
          const sessionId = data.sessionId;
          
          // Create or retrieve active session
          if (!activeSessions.has(sessionId)) {
            const session = await storage.getTherapySession(sessionId);
            if (!session) {
              socket.send(JSON.stringify({ 
                type: 'error', 
                message: 'Session not found' 
              }));
              return;
            }
            
            activeSession = {
              userId,
              sessionId,
              socket,
              messages: []
            };
            activeSessions.set(sessionId, activeSession);
          } else {
            activeSession = activeSessions.get(sessionId)!;
          }
          
          socket.send(JSON.stringify({ 
            type: 'joined', 
            sessionId 
          }));
        } 
        else if (data.type === 'message' && activeSession) {
          // Process a user message in a therapy session
          const content = data.content;
          
          // Store user message
          const userMessage: TherapyMessage = {
            role: 'user',
            content,
            timestamp: new Date()
          };
          
          activeSession.messages.push(userMessage);
          
          // Save message to database
          await storage.createMessage({
            sessionId: activeSession.sessionId,
            role: 'user',
            content,
            timestamp: new Date()
          });
          
          // Send typing indicator
          socket.send(JSON.stringify({ 
            type: 'typing', 
            isTyping: true 
          }));
          
          // Get therapist response
          const responseText = await getTherapistResponse(activeSession.messages);
          
          // Store therapist message
          const therapistMessage: TherapyMessage = {
            role: 'therapist',
            content: responseText,
            timestamp: new Date()
          };
          
          activeSession.messages.push(therapistMessage);
          
          // Save therapist message to database
          await storage.createMessage({
            sessionId: activeSession.sessionId,
            role: 'therapist',
            content: responseText,
            timestamp: new Date()
          });
          
          // End typing indicator
          socket.send(JSON.stringify({ 
            type: 'typing', 
            isTyping: false 
          }));
          
          // Send the text response
          socket.send(JSON.stringify({
            type: 'message',
            role: 'therapist',
            content: responseText,
            timestamp: new Date()
          }));
          
          // Generate and send audio if available
          const audioBuffer = await textToSpeech(responseText);
          if (audioBuffer) {
            const base64Audio = audioBuffer.toString('base64');
            socket.send(JSON.stringify({
              type: 'audio',
              data: base64Audio
            }));
          }
        } 
        else if (data.type === 'end' && activeSession) {
          // End session
          await storage.updateTherapySession(activeSession.sessionId, {
            status: 'completed',
            endTime: new Date()
          });
          
          activeSessions.delete(activeSession.sessionId);
          activeSession = null;
          
          socket.send(JSON.stringify({ 
            type: 'session_ended' 
          }));
        }
      } catch (error) {
        console.error('WebSocket error:', error);
        socket.send(JSON.stringify({ 
          type: 'error', 
          message: 'Internal server error' 
        }));
      }
    });

    socket.on('close', () => {
      console.log('WebSocket client disconnected');
      if (activeSession) {
        activeSessions.delete(activeSession.sessionId);
      }
    });
  });

  // Authentication routes
  app.post('/api/auth/login', async (req: Request, res: Response) => {
    const { username, password } = req.body;
    
    try {
      const user = await storage.getUserByUsername(username);
      
      if (!user) {
        return res.status(401).json({ error: 'Invalid username or password' });
      }
      
      // In a real app, we would verify the password hash here
      // For demo purposes, we're accepting any password
      
      // Log in the user
      req.login(user, (err) => {
        if (err) {
          return res.status(500).json({ error: 'Login failed' });
        }
        return res.json(user);
      });
    } catch (error) {
      return res.status(500).json({ error: 'Internal server error' });
    }
  });
  
  app.post('/api/auth/logout', (req: Request, res: Response) => {
    req.logout(() => {
      res.json({ success: true });
    });
  });

  // User routes
  app.post('/api/users', async (req: Request, res: Response) => {
    try {
      const userData = insertUserSchema.parse(req.body);
      const user = await storage.createUser(userData);
      res.status(201).json(user);
    } catch (error) {
      if (error instanceof z.ZodError) {
        return res.status(400).json({ error: error.errors });
      }
      res.status(500).json({ error: 'Failed to create user' });
    }
  });

  app.get('/api/users/:id', async (req: Request, res: Response) => {
    try {
      const userId = parseInt(req.params.id);
      const user = await storage.getUser(userId);
      
      if (!user) {
        return res.status(404).json({ error: 'User not found' });
      }
      
      res.json(user);
    } catch (error) {
      res.status(500).json({ error: 'Failed to fetch user' });
    }
  });

  // Therapy session routes
  app.post('/api/sessions', async (req: Request, res: Response) => {
    try {
      const sessionData = insertTherapySessionSchema.parse(req.body);
      const session = await storage.createTherapySession(sessionData);
      res.status(201).json(session);
    } catch (error) {
      if (error instanceof z.ZodError) {
        return res.status(400).json({ error: error.errors });
      }
      res.status(500).json({ error: 'Failed to create therapy session' });
    }
  });

  app.get('/api/users/:userId/sessions', async (req: Request, res: Response) => {
    try {
      const userId = parseInt(req.params.userId);
      const sessions = await storage.getTherapySessionsByUserId(userId);
      res.json(sessions);
    } catch (error) {
      res.status(500).json({ error: 'Failed to fetch therapy sessions' });
    }
  });

  app.get('/api/sessions/:id', async (req: Request, res: Response) => {
    try {
      const sessionId = parseInt(req.params.id);
      const session = await storage.getTherapySession(sessionId);
      
      if (!session) {
        return res.status(404).json({ error: 'Therapy session not found' });
      }
      
      res.json(session);
    } catch (error) {
      res.status(500).json({ error: 'Failed to fetch therapy session' });
    }
  });

  app.get('/api/sessions/:id/messages', async (req: Request, res: Response) => {
    try {
      const sessionId = parseInt(req.params.id);
      const messages = await storage.getMessagesBySessionId(sessionId);
      res.json(messages);
    } catch (error) {
      res.status(500).json({ error: 'Failed to fetch messages' });
    }
  });

  // Mood check routes
  app.post('/api/mood-checks', async (req: Request, res: Response) => {
    try {
      const moodCheckData = insertMoodCheckSchema.parse(req.body);
      const moodCheck = await storage.createMoodCheck(moodCheckData);
      res.status(201).json(moodCheck);
    } catch (error) {
      if (error instanceof z.ZodError) {
        return res.status(400).json({ error: error.errors });
      }
      res.status(500).json({ error: 'Failed to create mood check' });
    }
  });

  app.get('/api/users/:userId/mood-checks', async (req: Request, res: Response) => {
    try {
      const userId = parseInt(req.params.userId);
      const moodChecks = await storage.getMoodChecksByUserId(userId);
      res.json(moodChecks);
    } catch (error) {
      res.status(500).json({ error: 'Failed to fetch mood checks' });
    }
  });

  return httpServer;
}
```

### server/storage.ts
```typescript
import { 
  users, type User, type InsertUser,
  therapySessions, type TherapySession, type InsertTherapySession,
  messages, type Message, type InsertMessage,
  moodChecks, type MoodCheck, type InsertMoodCheck
} from "@shared/schema";

export interface IStorage {
  // User methods
  getUser(id: number): Promise<User | undefined>;
  getUserByUsername(username: string): Promise<User | undefined>;
  createUser(user: InsertUser): Promise<User>;
  
  // Therapy session methods
  getTherapySession(id: number): Promise<TherapySession | undefined>;
  getTherapySessionsByUserId(userId: number): Promise<TherapySession[]>;
  createTherapySession(session: InsertTherapySession): Promise<TherapySession>;
  updateTherapySession(id: number, session: Partial<InsertTherapySession>): Promise<TherapySession | undefined>;
  
  // Message methods
  getMessage(id: number): Promise<Message | undefined>;
  getMessagesBySessionId(sessionId: number): Promise<Message[]>;
  createMessage(message: InsertMessage): Promise<Message>;
  
  // Mood check methods
  getMoodCheck(id: number): Promise<MoodCheck | undefined>;
  getMoodChecksByUserId(userId: number): Promise<MoodCheck[]>;
  getMoodChecksBySessionId(sessionId: number): Promise<MoodCheck[]>;
  createMoodCheck(moodCheck: InsertMoodCheck): Promise<MoodCheck>;
}

export class MemStorage implements IStorage {
  private users: Map<number, User>;
  private therapySessions: Map<number, TherapySession>;
  private messages: Map<number, Message>;
  private moodChecks: Map<number, MoodCheck>;
  private userIdCounter: number;
  private sessionIdCounter: number;
  private messageIdCounter: number;
  private moodCheckIdCounter: number;

  constructor() {
    this.users = new Map();
    this.therapySessions = new Map();
    this.messages = new Map();
    this.moodChecks = new Map();
    this.userIdCounter = 1;
    this.sessionIdCounter = 1;
    this.messageIdCounter = 1;
    this.moodCheckIdCounter = 1;
    
    // Add a default user
    this.createUser({
      username: "sarahjohnson",
      password: "password123",
      fullName: "Sarah Johnson",
      email: "sarah@example.com",
      profileImageUrl: "https://randomuser.me/api/portraits/women/72.jpg"
    });
  }

  async getUser(id: number): Promise<User | undefined> {
    return this.users.get(id);
  }

  async getUserByUsername(username: string): Promise<User | undefined> {
    for (const user of this.users.values()) {
      if (user.username === username) {
        return user;
      }
    }
    return undefined;
  }

  async createUser(insertUser: InsertUser): Promise<User> {
    const id = this.userIdCounter++;
    const user: User = { 
      id, 
      ...insertUser 
    };
    this.users.set(id, user);
    return user;
  }

  async getTherapySession(id: number): Promise<TherapySession | undefined> {
    return this.therapySessions.get(id);
  }

  async getTherapySessionsByUserId(userId: number): Promise<TherapySession[]> {
    const sessions: TherapySession[] = [];
    for (const session of this.therapySessions.values()) {
      if (session.userId === userId) {
        sessions.push(session);
      }
    }
    return sessions;
  }

  async createTherapySession(session: InsertTherapySession): Promise<TherapySession> {
    const id = this.sessionIdCounter++;
    const therapySession: TherapySession = { 
      id, 
      ...session 
    };
    this.therapySessions.set(id, therapySession);
    return therapySession;
  }

  async updateTherapySession(id: number, sessionUpdate: Partial<InsertTherapySession>): Promise<TherapySession | undefined> {
    const existingSession = this.therapySessions.get(id);
    if (!existingSession) {
      return undefined;
    }
    
    const updatedSession: TherapySession = { ...existingSession, ...sessionUpdate };
    this.therapySessions.set(id, updatedSession);
    return updatedSession;
  }

  async getMessage(id: number): Promise<Message | undefined> {
    return this.messages.get(id);
  }

  async getMessagesBySessionId(sessionId: number): Promise<Message[]> {
    const sessionMessages: Message[] = [];
    for (const message of this.messages.values()) {
      if (message.sessionId === sessionId) {
        sessionMessages.push(message);
      }
    }
    return sessionMessages;
  }

  async createMessage(message: InsertMessage): Promise<Message> {
    const id = this.messageIdCounter++;
    const newMessage: Message = { ...message, id };
    this.messages.set(id, newMessage);
    return newMessage;
  }

  async getMoodCheck(id: number): Promise<MoodCheck | undefined> {
    return this.moodChecks.get(id);
  }

  async getMoodChecksByUserId(userId: number): Promise<MoodCheck[]> {
    const userMoodChecks: MoodCheck[] = [];
    for (const moodCheck of this.moodChecks.values()) {
      if (moodCheck.userId === userId) {
        userMoodChecks.push(moodCheck);
      }
    }
    return userMoodChecks;
  }

  async getMoodChecksBySessionId(sessionId: number): Promise<MoodCheck[]> {
    const sessionMoodChecks: MoodCheck[] = [];
    for (const moodCheck of this.moodChecks.values()) {
      if (moodCheck.sessionId === sessionId) {
        sessionMoodChecks.push(moodCheck);
      }
    }
    return sessionMoodChecks;
  }

  async createMoodCheck(moodCheck: InsertMoodCheck): Promise<MoodCheck> {
    const id = this.moodCheckIdCounter++;
    const newMoodCheck: MoodCheck = { 
      id, 
      ...moodCheck 
    };
    this.moodChecks.set(id, newMoodCheck);
    return newMoodCheck;
  }
}

export const storage = new MemStorage();
```

### server/vite.ts
```typescript
import { Express } from "express";
import { createServer } from "vite";
import { Server } from "http";
import path from "path";
import fs from "fs";

export function log(message: string, source = "express") {
  const time = new Date().toLocaleTimeString();
  console.log(`${time} [${source}] ${message}`);
}

export async function setupVite(app: Express, server: Server) {
  const viteServer = await createServer({
    server: {
      middlewareMode: true,
      hmr: {
        server: server,
      },
    },
    appType: "spa",
  });

  app.use(viteServer.middlewares);

  app.get("*", async (req, res, next) => {
    try {
      const url = req.originalUrl;

      // Skip API requests
      if (url.startsWith("/api/")) {
        return next();
      }

      // Serve the client entry point for all other routes
      let template = fs.readFileSync(
        path.resolve("./client/index.html"),
        "utf-8"
      );
      template = await viteServer.transformIndexHtml(url, template);
      res.status(200).set({ "Content-Type": "text/html" }).end(template);
    } catch (e) {
      viteServer.ssrFixStacktrace(e as Error);
      next(e);
    }
  });
}

export function serveStatic(app: Express) {
  app.use(express.static(path.resolve("./dist")));

  // Serve index.html for all non-API routes (for SPA routing)
  app.get("*", (req, res, next) => {
    if (req.path.startsWith("/api/")) {
      return next();
    }

    res.sendFile(path.resolve("./dist/index.html"));
  });
}
```

### server/db.ts
```typescript
import { drizzle } from "drizzle-orm/postgres-js";
import { Pool } from "pg";
import * as schema from '@shared/schema';

export const pool = new Pool({ connectionString: process.env.DATABASE_URL });
export const db = drizzle({ client: pool, schema });
```

### server/lib/openai.ts
```typescript
import OpenAI from "openai";
import { getTherapistResponseFromAnthropic } from "./anthropic";

// the newest OpenAI model is "gpt-4o" which was released May 13, 2024. do not change this unless explicitly requested by the user
const openai = new OpenAI({ 
  apiKey: process.env.OPENAI_API_KEY 
});

export const MODEL_NAME = "gpt-4o";

// Define system prompt for therapy conversations
const THERAPY_SYSTEM_PROMPT = `
You are an AI therapist named Dr. Michelle Carter, with expertise in cognitive behavioral therapy (CBT) techniques. 
Your role is to help users address mental health concerns through supportive conversation and evidence-based approaches.

Guidelines for your responses:
1. Lead with empathy, warmth, and understanding
2. Use CBT frameworks when appropriate
3. Ask thoughtful follow-up questions that encourage self-reflection
4. Provide practical coping strategies and techniques
5. Maintain appropriate professional boundaries
6. Never diagnose medical or psychiatric conditions
7. Suggest seeking professional in-person help for serious concerns
8. Keep responses concise (50-100 words) and conversational in tone
9. Speak as if in a video call format with occasional filler words for realism

Your primary goal is to create a supportive space while offering evidence-based guidance.
`;

export interface TherapyMessage {
  role: "user" | "therapist";
  content: string;
  timestamp: Date;
}

export async function getTherapistResponse(messages: TherapyMessage[]): Promise<string> {
  if (!process.env.OPENAI_API_KEY) {
    console.warn("OPENAI_API_KEY not found, trying Anthropic fallback");
    return getTherapistResponseFromAnthropic(messages);
  }
  
  try {
    // Verify API key exists and is not empty
    if (!process.env.OPENAI_API_KEY || process.env.OPENAI_API_KEY.trim() === '') {
      console.warn("OpenAI API key is missing or empty");
      return getTherapistResponseFromAnthropic(messages);
    }
    
    // Convert our message format to OpenAI message format
    const openaiMessages = [
      { role: "system" as const, content: THERAPY_SYSTEM_PROMPT },
      ...messages.map(msg => ({
        role: msg.role === "user" ? "user" as const : "assistant" as const,
        content: msg.content
      }))
    ];

    const response = await openai.chat.completions.create({
      model: MODEL_NAME,
      messages: openaiMessages,
      temperature: 0.7,
      max_tokens: 300,
    });

    return response.choices[0].message.content || "I'm sorry, I'm having trouble responding right now.";
  } catch (error: any) {
    console.error("Error getting therapist response from OpenAI:", error);
    
    // Check for specific API key issues
    if (error?.code === 'insufficient_quota' || 
        error?.status === 429 || 
        (error?.message && error?.message.includes('quota'))) {
      console.warn("OpenAI API quota exceeded or key issue detected");
    }
    
    // Try Anthropic as fallback
    console.log("Trying Anthropic fallback...");
    return getTherapistResponseFromAnthropic(messages);
  }
}
```

### server/lib/anthropic.ts
```typescript
import Anthropic from '@anthropic-ai/sdk';

// the newest Anthropic model is "claude-3-7-sonnet-20250219" which was released February 24, 2025
const anthropic = new Anthropic({
  apiKey: process.env.ANTHROPIC_API_KEY || '',
});

// Define system prompt for therapy conversations
const THERAPY_SYSTEM_PROMPT = `
You are an AI therapist named Dr. Michelle Carter, with expertise in cognitive behavioral therapy (CBT) techniques. 
Your role is to help users address mental health concerns through supportive conversation and evidence-based approaches.

Guidelines for your responses:
1. Lead with empathy, warmth, and understanding
2. Use CBT frameworks when appropriate
3. Ask thoughtful follow-up questions that encourage self-reflection
4. Provide practical coping strategies and techniques
5. Maintain appropriate professional boundaries
6. Never diagnose medical or psychiatric conditions
7. Suggest seeking professional in-person help for serious concerns
8. Keep responses concise (50-100 words) and conversational in tone
9. Speak as if in a video call format with occasional filler words for realism

Your primary goal is to create a supportive space while offering evidence-based guidance.
`;

import { TherapyMessage } from './openai';

export async function getTherapistResponseFromAnthropic(messages: TherapyMessage[]): Promise<string> {
  if (!process.env.ANTHROPIC_API_KEY) {
    console.warn('ANTHROPIC_API_KEY not found, using fallback response');
    return "I'm here to support you. How are you feeling today, and what's been on your mind? Remember, I'm here to listen and help you work through whatever you're experiencing.";
  }
  
  try {
    // Convert our message format to Anthropic message format
    const anthropicMessages = messages.map(msg => ({
      role: msg.role === "user" ? "user" as const : "assistant" as const,
      content: msg.content
    }));

    const response = await anthropic.messages.create({
      model: 'claude-3-7-sonnet-20250219',
      max_tokens: 1024,
      system: THERAPY_SYSTEM_PROMPT,
      messages: anthropicMessages,
    });

    // Check if content exists and is of text type
    if (response.content?.[0] && 'text' in response.content[0]) {
      return response.content[0].text;
    }
    
    return "I'm here to help. What would you like to discuss today?";
  } catch (error) {
    console.error("Error getting therapist response from Anthropic:", error);
    return "I apologize, but I'm experiencing some technical difficulties. Let's try again in a moment.";
  }
}
```

### server/lib/elevenlabs.ts
```typescript
import fetch from 'node-fetch';

// ElevenLabs API configuration
const API_KEY = process.env.ELEVENLABS_API_KEY || 'dummy-key';
const API_URL = 'https://api.elevenlabs.io/v1';

// Therapist voice ID - using a female voice for "Dr. Michelle Carter"
// Using the "Rachel" voice (premium) or falling back to "Bella" (a default voice)
// ElevenLabs default voices: 21m00Tcm4TlvDq8ikWAM (Rachel), EXAVITQu4vr4xnSDxMaL (Bella)
const VOICE_ID = 'EXAVITQu4vr4xnSDxMaL';

// Text to speech conversion
export async function textToSpeech(text: string): Promise<Buffer | null> {
  try {
    // Verify API key exists and is not empty
    if (!process.env.ELEVENLABS_API_KEY || process.env.ELEVENLABS_API_KEY === 'dummy-key' || process.env.ELEVENLABS_API_KEY.trim() === '') {
      console.warn("ElevenLabs API key is missing or invalid");
      return null;
    }
    
    // Configure the voice settings
    const voiceSettings = {
      stability: 0.75,
      similarity_boost: 0.75,
      style: 0.0,
      use_speaker_boost: true
    };

    // First, verify the voice exists by checking available voices
    try {
      const voices = await getAvailableVoices();
      if (voices && Array.isArray(voices.voices)) {
        const voiceExists = voices.voices.some((voice: any) => voice.voice_id === VOICE_ID);
        if (!voiceExists) {
          // If our voice isn't found, try to use any available voice
          if (voices.voices.length > 0) {
            console.warn(`Voice ID ${VOICE_ID} not found, falling back to first available voice`);
            const fallbackVoiceId = voices.voices[0].voice_id;
            const fallbackResponse = await makeTextToSpeechRequest(text, fallbackVoiceId, voiceSettings);
            if (fallbackResponse) return fallbackResponse;
          }
          console.error("No valid voices found in ElevenLabs account");
          return null;
        }
      }
    } catch (voiceCheckError) {
      console.warn("Failed to check voice availability, proceeding with request anyway:", voiceCheckError);
    }

    return await makeTextToSpeechRequest(text, VOICE_ID, voiceSettings);
  } catch (error) {
    console.error('Error in ElevenLabs text-to-speech:', error);
    return null;
  }
}

// Helper function to make the actual TTS request
async function makeTextToSpeechRequest(text: string, voiceId: string, voiceSettings: any): Promise<Buffer | null> {
  const response = await fetch(`${API_URL}/text-to-speech/${voiceId}`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'xi-api-key': API_KEY
    },
    body: JSON.stringify({
      text,
      model_id: 'eleven_multilingual_v2',
      voice_settings: voiceSettings
    })
  });

  if (!response.ok) {
    const errorText = await response.text();
    console.error(`ElevenLabs API error: ${response.status} ${errorText}`);
    return null;
  }

  // Response is an audio file
  const audioBuffer = await response.arrayBuffer();
  return Buffer.from(audioBuffer);
}

// Get available voices (for testing/admin purposes)
export async function getAvailableVoices(): Promise<any> {
  try {
    const response = await fetch(`${API_URL}/voices`, {
      method: 'GET',
      headers: {
        'xi-api-key': API_KEY
      }
    });

    if (!response.ok) {
      const errorText = await response.text();
      console.error(`ElevenLabs API error: ${response.status} ${errorText}`);
      return null;
    }

    return await response.json();
  } catch (error) {
    console.error('Error fetching available voices:', error);
    return null;
  }
}
```

## Shared Files

### shared/schema.ts
```typescript
import { pgTable, serial, text, timestamp, varchar, integer, boolean } from "drizzle-orm/pg-core";
import { createInsertSchema } from "drizzle-zod";
import { z } from "zod";

// User model
export const users = pgTable("users", {
  id: serial("id").primaryKey(),
  username: varchar("username", { length: 255 }).notNull().unique(),
  password: varchar("password", { length: 255 }).notNull(),
  fullName: varchar("full_name", { length: 255 }).notNull(),
  email: varchar("email", { length: 255 }).notNull().unique(),
  profileImageUrl: text("profile_image_url"),
  createdAt: timestamp("created_at").defaultNow().notNull(),
});

export const insertUserSchema = createInsertSchema(users).pick({
  username: true,
  password: true,
  fullName: true,
  email: true,
  profileImageUrl: true,
});

export type InsertUser = z.infer<typeof insertUserSchema>;
export type User = typeof users.$inferSelect;

// Therapy session model
export const therapySessions = pgTable("therapy_sessions", {
  id: serial("id").primaryKey(),
  userId: integer("user_id").notNull().references(() => users.id),
  startTime: timestamp("start_time").defaultNow().notNull(),
  endTime: timestamp("end_time"),
  status: varchar("status", { length: 20 }).notNull().default("scheduled"),
  focus: text("focus"),
  notes: text("notes"),
  createdAt: timestamp("created_at").defaultNow().notNull(),
});

export const insertTherapySessionSchema = createInsertSchema(therapySessions).pick({
  userId: true,
  startTime: true,
  endTime: true,
  status: true,
  focus: true,
  notes: true,
});

export type InsertTherapySession = z.infer<typeof insertTherapySessionSchema>;
export type TherapySession = typeof therapySessions.$inferSelect;

// Message model for chat messages during therapy sessions
export const messages = pgTable("messages", {
  id: serial("id").primaryKey(),
  sessionId: integer("session_id").notNull().references(() => therapySessions.id),
  role: varchar("role", { length: 20 }).notNull(),
  content: text("content").notNull(),
  timestamp: timestamp("timestamp").defaultNow().notNull(),
  createdAt: timestamp("created_at").defaultNow().notNull(),
});

export const insertMessageSchema = createInsertSchema(messages).pick({
  sessionId: true,
  role: true,
  content: true,
  timestamp: true,
});

export type InsertMessage = z.infer<typeof insertMessageSchema>;
export type Message = typeof messages.$inferSelect;

// Mood check model for tracking user mood
export const moodChecks = pgTable("mood_checks", {
  id: serial("id").primaryKey(),
  userId: integer("user_id").notNull().references(() => users.id),
  sessionId: integer("session_id").references(() => therapySessions.id),
  score: integer("score").notNull(),
  notes: text("notes"),
  timestamp: timestamp("timestamp").defaultNow().notNull(),
  createdAt: timestamp("created_at").defaultNow().notNull(),
});

export const insertMoodCheckSchema = createInsertSchema(moodChecks).pick({
  userId: true,
  sessionId: true,
  score: true,
  notes: true,
  timestamp: true,
});

export type InsertMoodCheck = z.infer<typeof insertMoodCheckSchema>;
export type MoodCheck = typeof moodChecks.$inferSelect;
```

## Client Files

### client/index.html
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>AI Therapy Platform</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/client/src/main.tsx"></script>
  </body>
</html>
```

### client/src/main.tsx
```typescript
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';
import './index.css';
import { QueryClientProvider } from '@tanstack/react-query';
import { queryClient } from '@lib/queryClient';
import { Toaster } from '@/components/ui/toaster';
import { UserProvider } from './contexts/UserContext';
import { TherapySessionProvider } from './contexts/TherapySessionContext';

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <QueryClientProvider client={queryClient}>
      <UserProvider>
        <TherapySessionProvider>
          <App />
          <Toaster />
        </TherapySessionProvider>
      </UserProvider>
    </QueryClientProvider>
  </React.StrictMode>,
);
```

### client/src/index.css
```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 222.2 84% 4.9%;

    --card: 0 0% 100%;
    --card-foreground: 222.2 84% 4.9%;

    --popover: 0 0% 100%;
    --popover-foreground: 222.2 84% 4.9%;

    --primary: 250 84% 54%;
    --primary-foreground: 210 40% 98%;

    --secondary: 210 40% 96.1%;
    --secondary-foreground: 222.2 47.4% 11.2%;

    --muted: 210 40% 96.1%;
    --muted-foreground: 215.4 16.3% 46.9%;

    --accent: 210 40% 96.1%;
    --accent-foreground: 222.2 47.4% 11.2%;

    --destructive: 0 84.2% 60.2%;
    --destructive-foreground: 210 40% 98%;

    --border: 214.3 31.8% 91.4%;
    --input: 214.3 31.8% 91.4%;
    --ring: 222.2 84% 4.9%;

    --radius: 0.6rem;
  }

  .dark {
    --background: 222.2 84% 4.9%;
    --foreground: 210 40% 98%;

    --card: 222.2 84% 4.9%;
    --card-foreground: 210 40% 98%;

    --popover: 222.2 84% 4.9%;
    --popover-foreground: 210 40% 98%;

    --primary: 250 84% 54%;
    --primary-foreground: 210 40% 98%;

    --secondary: 217.2 32.6% 17.5%;
    --secondary-foreground: 210 40% 98%;

    --muted: 217.2 32.6% 17.5%;
    --muted-foreground: 215 20.2% 65.1%;

    --accent: 217.2 32.6% 17.5%;
    --accent-foreground: 210 40% 98%;

    --destructive: 0 62.8% 30.6%;
    --destructive-foreground: 210 40% 98%;

    --border: 217.2 32.6% 17.5%;
    --input: 217.2 32.6% 17.5%;
    --ring: 212.7 26.8% 83.9%;
  }
}

@layer base {
  * {
    @apply border-border;
  }
  body {
    @apply bg-background text-foreground;
  }
}

.background-gradient {
  background: radial-gradient(circle at top right, rgb(var(--primary)), rgb(var(--background)));
}

.logo-gradient {
  background: linear-gradient(to right, hsl(var(--primary)), hsl(var(--primary) / 0.8));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.card-hover {
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.card-hover:hover {
  transform: translateY(-5px);
  box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.1);
}

.avatar-container {
  height: 350px;
  width: 100%;
  position: relative;
  overflow: hidden;
  border-radius: 12px;
  background-color: #f5f5f5;
}

.avatar-image {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.waveform-container {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 3px;
  height: 60px;
}

.waveform-bar {
  width: 4px;
  border-radius: 2px;
  background-color: hsl(var(--primary));
  transition: height 0.2s ease;
}

.pulse-animation {
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0% {
    transform: scale(1);
    opacity: 1;
  }
  50% {
    transform: scale(1.1);
    opacity: 0.7;
  }
  100% {
    transform: scale(1);
    opacity: 1;
  }
}
```

### client/src/App.tsx
```typescript
import { Route, Switch } from 'wouter';
import Home from './pages/Home';
import PastSessions from './pages/PastSessions';
import TherapySession from './components/TherapySession';
import Profile from './pages/Profile';
import Settings from './pages/Settings';
import Progress from './pages/Progress';
import Schedule from './pages/Schedule';
import NotFound from './pages/not-found';
import Help from './pages/Help';
import Sidebar from './components/Sidebar';
import { useUser } from './contexts/UserContext';
import { useEffect, useState } from 'react';
import { Button } from './components/ui/button';
import { 
  Card, 
  CardContent, 
  CardDescription, 
  CardFooter, 
  CardHeader, 
  CardTitle 
} from './components/ui/card';
import { Input } from './components/ui/input';
import { Label } from './components/ui/label';
import { useToast } from './hooks/use-toast';

function App() {
  const { user, login, register, loading } = useUser();
  const [authMode, setAuthMode] = useState<'login' | 'register'>('login');
  const [username, setUsername] = useState('');
  const [password, setPassword] = useState('');
  const [fullName, setFullName] = useState('');
  const [email, setEmail] = useState('');
  const [loginError, setLoginError] = useState('');
  const { toast } = useToast();

  useEffect(() => {
    // Auto-login for demo purposes
    if (!user && !loading) {
      handleLogin();
    }
  }, [loading]);

  const handleLogin = async () => {
    setLoginError('');
    try {
      // Use demo credentials if none provided
      const loginUsername = username || 'sarahjohnson';
      const loginPassword = password || 'password123';
      
      await login(loginUsername, loginPassword);
    } catch (error) {
      console.error('Login error:', error);
      setLoginError('Invalid credentials. Please try again.');
    }
  };

  const handleRegister = async () => {
    setLoginError('');
    try {
      if (!username || !password || !fullName || !email) {
        setLoginError('All fields are required');
        return;
      }
      
      await register({
        username,
        password,
        fullName,
        email,
      });
      
      toast({
        title: "Account created!",
        description: "Welcome to the AI Therapy Platform.",
      });
    } catch (error) {
      console.error('Registration error:', error);
      setLoginError('Failed to create account. Please try again.');
    }
  };

  const toggleAuthMode = () => {
    setAuthMode(authMode === 'login' ? 'register' : 'login');
    setLoginError('');
  };

  if (loading) {
    return (
      <div className="flex items-center justify-center min-h-screen">
        <div className="w-8 h-8 border-4 border-primary border-solid rounded-full border-t-transparent animate-spin"></div>
      </div>
    );
  }

  if (!user) {
    return (
      <div className="flex min-h-screen items-center justify-center bg-gradient-to-tr from-muted to-background p-4">
        <Card className="w-full max-w-md shadow-lg">
          <CardHeader className="space-y-1">
            <CardTitle className="text-3xl font-bold text-center">
              <span className="logo-gradient">AI Therapy</span>
            </CardTitle>
            <CardDescription className="text-center">
              {authMode === 'login' 
                ? 'Enter your credentials to access your account' 
                : 'Create an account to get started'}
            </CardDescription>
          </CardHeader>
          <CardContent className="space-y-4">
            {loginError && (
              <div className="p-3 mb-3 text-sm text-white bg-destructive rounded">
                {loginError}
              </div>
            )}
            
            {authMode === 'register' && (
              <>
                <div className="space-y-2">
                  <Label htmlFor="fullName">Full Name</Label>
                  <Input 
                    id="fullName" 
                    placeholder="Enter your full name" 
                    value={fullName}
                    onChange={(e) => setFullName(e.target.value)}
                  />
                </div>
                <div className="space-y-2">
                  <Label htmlFor="email">Email</Label>
                  <Input 
                    id="email" 
                    type="email" 
                    placeholder="example@domain.com" 
                    value={email}
                    onChange={(e) => setEmail(e.target.value)}
                  />
                </div>
              </>
            )}
            
            <div className="space-y-2">
              <Label htmlFor="username">Username</Label>
              <Input 
                id="username" 
                placeholder="Enter your username" 
                value={username}
                onChange={(e) => setUsername(e.target.value)}
              />
            </div>
            <div className="space-y-2">
              <Label htmlFor="password">Password</Label>
              <Input 
                id="password" 
                type="password" 
                placeholder="Enter your password" 
                value={password}
                onChange={(e) => setPassword(e.target.value)}
              />
            </div>
          </CardContent>
          <CardFooter className="flex flex-col space-y-4">
            <Button 
              className="w-full"
              onClick={authMode === 'login' ? handleLogin : handleRegister}
            >
              {authMode === 'login' ? 'Sign In' : 'Create Account'}
            </Button>
            <div className="text-center text-sm">
              {authMode === 'login' ? "Don't have an account? " : "Already have an account? "}
              <button 
                onClick={toggleAuthMode}
                className="text-primary underline hover:text-primary/80"
              >
                {authMode === 'login' ? 'Sign Up' : 'Sign In'}
              </button>
            </div>
          </CardFooter>
        </Card>
      </div>
    );
  }

  return (
    <div className="flex min-h-screen bg-background">
      <Sidebar />
      
      <div className="flex-1 flex flex-col">
        <main className="flex-1 overflow-y-auto p-6">
          <Switch>
            <Route path="/" component={Home} />
            <Route path="/session/:id?" component={TherapySession} />
            <Route path="/past-sessions" component={PastSessions} />
            <Route path="/profile" component={Profile} />
            <Route path="/settings" component={Settings} />
            <Route path="/progress" component={Progress} />
            <Route path="/schedule" component={Schedule} />
            <Route path="/help" component={Help} />
            <Route component={NotFound} />
          </Switch>
        </main>
      </div>
    </div>
  );
}

export default App;
```

### client/src/pages/not-found.tsx
```typescript
import { Button } from "@/components/ui/button";
import { Link } from "wouter";

export default function NotFound() {
  return (
    <div className="flex flex-col items-center justify-center min-h-[80vh] px-4 text-center">
      <h1 className="text-9xl font-extrabold text-gray-700">404</h1>
      <h2 className="mb-8 text-3xl font-semibold">Page Not Found</h2>
      <p className="mb-8 text-lg text-muted-foreground max-w-md">
        Sorry, we couldn't find the page you're looking for. It might have been moved or deleted.
      </p>
      <Link href="/">
        <Button>Return to Home</Button>
      </Link>
    </div>
  );
}
```

### client/src/lib/queryClient.ts
```typescript
import { QueryClient } from "@tanstack/react-query";

async function throwIfResNotOk(res: Response) {
  if (!res.ok) {
    let errorText = "";
    try {
      const error = await res.json();
      errorText = error.error || res.statusText;
    } catch (e) {
      errorText = res.statusText;
    }
    const error = new Error(errorText);
    throw error;
  }
}

export async function apiRequest(
  input: RequestInfo | URL,
  init: RequestInit = {},
  options: { returnNullOnUnauthorized?: boolean } = {}
) {
  const res = await fetch(input, {
    ...init,
    headers: {
      ...init.headers,
      "Content-Type": "application/json",
    },
  });
  
  if (res.status === 401 && options.returnNullOnUnauthorized) {
    return null;
  }
  
  await throwIfResNotOk(res);
  
  if (res.status === 204) {
    return null;
  }

  return res.json();
}

type UnauthorizedBehavior = "returnNull" | "throw";
export const getQueryFn: <T>(options: {
  on401: UnauthorizedBehavior;
}) => (context: { queryKey: Array<string | number | boolean> }) => Promise<T> =
  (options) => async (context) => {
    const [url, ...params] = context.queryKey;
    const urlWithParams = params.length > 0 ? `${url}?${new URLSearchParams(
      params
      // Allow booleans and null values to be serialized to URL
        .map((param: any) => (typeof param === "object" ? Object.entries(param).filter(([_, v]) => v !== undefined)
            .map(([k, v]) => [k, v === null ? "" : v])
            : []))
        .flat()
        .map(([k, v]) => [k, String(v)])
    )}` : url;
    return apiRequest(urlWithParams, undefined, {
      returnNullOnUnauthorized: options.on401 === "returnNull",
    });
  };

export const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      queryFn: getQueryFn({
        on401: "throw",
      }),
      retry: false,
      refetchOnWindowFocus: false,
    },
  },
});
```

### client/src/lib/webSocket.ts
```typescript
export const createWebSocketConnection = (): WebSocket => {
  const protocol = window.location.protocol === 'https:' ? 'wss:' : 'ws:';
  const wsUrl = `${protocol}//${window.location.host}/ws`;
  const socket = new WebSocket(wsUrl);
  
  socket.onopen = () => {
    console.log('WebSocket connection established');
  };
  
  socket.onerror = (error) => {
    console.error('WebSocket error:', error);
  };
  
  socket.onclose = () => {
    console.log('WebSocket connection closed');
  };
  
  return socket;
};

export const isWebSocketReady = (socket: WebSocket | null): boolean => {
  return socket !== null && socket.readyState === WebSocket.OPEN;
};
```

### client/src/contexts/TherapySessionContext.tsx
```typescript
import React, { createContext, useState, useContext, useEffect, ReactNode } from 'react';
import { useUser } from './UserContext';
import { createWebSocketConnection, isWebSocketReady } from '@lib/webSocket';
import { apiRequest } from '@lib/queryClient';
import { useToast } from '@hooks/use-toast';

export interface Message {
  role: 'user' | 'therapist';
  content: string;
  timestamp: Date;
}

export interface TherapySession {
  id: number;
  userId: number;
  startTime: Date;
  endTime?: Date;
  status: 'scheduled' | 'in-progress' | 'completed' | 'cancelled';
  focus?: string;
  notes?: string;
  messages: Message[];
}

interface TherapySessionContextType {
  activeSession: TherapySession | null;
  isLoading: boolean;
  isConnected: boolean;
  isTherapistTyping: boolean;
  startSession: (sessionId?: number, focus?: string) => Promise<void>;
  endSession: () => Promise<void>;
  sendMessage: (content: string) => Promise<void>;
  sessionTime: number;
}

const TherapySessionContext = createContext<TherapySessionContextType | undefined>(undefined);

export const TherapySessionProvider: React.FC<{ children: ReactNode }> = ({ children }) => {
  const { user } = useUser();
  const { toast } = useToast();
  
  const [socket, setSocket] = useState<WebSocket | null>(null);
  const [activeSession, setActiveSession] = useState<TherapySession | null>(null);
  const [isLoading, setIsLoading] = useState<boolean>(false);
  const [isConnected, setIsConnected] = useState<boolean>(false);
  const [isTherapistTyping, setIsTherapistTyping] = useState<boolean>(false);
  const [sessionStart, setSessionStart] = useState<Date | null>(null);
  const [sessionTime, setSessionTime] = useState<number>(0);
  const [audioPlayer, setAudioPlayer] = useState<HTMLAudioElement | null>(null);

  // Initialize audio player
  useEffect(() => {
    setAudioPlayer(new Audio());
  }, []);

  // Session timer
  useEffect(() => {
    let intervalId: number;
    
    if (activeSession && sessionStart) {
      intervalId = window.setInterval(() => {
        const now = new Date();
        const elapsed = Math.floor((now.getTime() - sessionStart.getTime()) / 1000);
        setSessionTime(elapsed);
      }, 1000);
    }
    
    return () => {
      if (intervalId) {
        clearInterval(intervalId);
      }
    };
  }, [activeSession, sessionStart]);

  // Handle WebSocket messages
  useEffect(() => {
    if (socket) {
      socket.onmessage = (event) => {
        const data = JSON.parse(event.data);
        
        switch (data.type) {
          case 'joined':
            setIsConnected(true);
            break;
          
          case 'message':
            if (activeSession) {
              const message: Message = {
                role: data.role,
                content: data.content,
                timestamp: new Date(data.timestamp)
              };
              
              setActiveSession(prev => {
                if (!prev) return null;
                return {
                  ...prev,
                  messages: [...prev.messages, message]
                };
              });
            }
            break;
          
          case 'typing':
            setIsTherapistTyping(data.isTyping);
            break;
          
          case 'audio':
            if (audioPlayer && data.data) {
              const audioSrc = `data:audio/mp3;base64,${data.data}`;
              audioPlayer.src = audioSrc;
              audioPlayer.play().catch(err => {
                console.error('Error playing audio:', err);
              });
            }
            break;
          
          case 'session_ended':
            setActiveSession(null);
            setIsConnected(false);
            setSessionStart(null);
            setSessionTime(0);
            break;
          
          case 'error':
            toast({
              title: "Session Error",
              description: data.message,
              variant: "destructive"
            });
            break;
        }
      };
    }
  }, [socket, activeSession, audioPlayer, toast]);

  const startSession = async (sessionId?: number, focus?: string) => {
    try {
      setIsLoading(true);
      
      if (!user) {
        throw new Error('User not authenticated');
      }
      
      let session;
      
      if (sessionId) {
        // Join existing session
        session = await apiRequest(`/api/sessions/${sessionId}`);
      } else {
        // Create new session
        const newSession = {
          userId: user.id,
          startTime: new Date(),
          status: 'in-progress',
          focus: focus || 'General therapy session'
        };
        
        session = await apiRequest('/api/sessions', {
          method: 'POST',
          body: JSON.stringify(newSession)
        });
      }
      
      // Initialize messages array if not present
      if (!session.messages) {
        session.messages = [];
      }
      
      const newSession: TherapySession = {
        ...session,
        startTime: new Date(session.startTime),
        endTime: session.endTime ? new Date(session.endTime) : undefined,
        messages: session.messages.map((msg: any) => ({
          ...msg,
          timestamp: new Date(msg.timestamp)
        }))
      };
      
      setActiveSession(newSession);
      setSessionStart(new Date());
      
      // Connect WebSocket
      const ws = createWebSocketConnection();
      
      ws.onopen = () => {
        // Join session when WebSocket is ready
        ws.send(JSON.stringify({
          type: 'join',
          userId: user.id,
          sessionId: newSession.id
        }));
      };
      
      setSocket(ws);
    } catch (error) {
      console.error('Error starting session:', error);
      toast({
        title: "Session Error",
        description: "Failed to start therapy session",
        variant: "destructive"
      });
    } finally {
      setIsLoading(false);
    }
  };

  const endSession = async () => {
    try {
      if (activeSession && socket && isWebSocketReady(socket)) {
        // End the session
        socket.send(JSON.stringify({
          type: 'end',
          sessionId: activeSession.id
        }));
        
        // Clean up
        socket.close();
        setSocket(null);
        setActiveSession(null);
        setIsConnected(false);
        setSessionStart(null);
        setSessionTime(0);
      }
    } catch (error) {
      console.error('Error ending session:', error);
    }
  };

  const sendMessage = async (content: string) => {
    try {
      if (!activeSession || !socket || !isWebSocketReady(socket)) {
        throw new Error('No active session or connection');
      }
      
      // Send message through WebSocket
      socket.send(JSON.stringify({
        type: 'message',
        content
      }));
      
      // Add user message to UI immediately
      const userMessage: Message = {
        role: 'user',
        content,
        timestamp: new Date()
      };
      
      setActiveSession(prev => {
        if (!prev) return null;
        return {
          ...prev,
          messages: [...prev.messages, userMessage]
        };
      });
    } catch (error) {
      console.error('Error sending message:', error);
      toast({
        title: "Message Error",
        description: "Failed to send message",
        variant: "destructive"
      });
    }
  };

  return (
    <TherapySessionContext.Provider
      value={{
        activeSession,
        isLoading,
        isConnected,
        isTherapistTyping,
        startSession,
        endSession,
        sendMessage,
        sessionTime
      }}
    >
      {children}
    </TherapySessionContext.Provider>
  );
};

export const useTherapySession = (): TherapySessionContextType => {
  const context = useContext(TherapySessionContext);
  if (context === undefined) {
    throw new Error('useTherapySession must be used within a TherapySessionProvider');
  }
  return context;
};
```

### client/src/contexts/UserContext.tsx
```typescript
import React, { createContext, useState, useContext, useEffect, ReactNode } from 'react';
import { apiRequest } from '@lib/queryClient';

interface User {
  id: number;
  username: string;
  fullName: string;
  email: string;
  profileImageUrl?: string;
}

interface UserContextType {
  user: User | null;
  loading: boolean;
  login: (username: string, password: string) => Promise<void>;
  logout: () => void;
  register: (userData: RegisterData) => Promise<void>;
}

interface RegisterData {
  username: string;
  password: string;
  fullName: string;
  email: string;
  profileImageUrl?: string;
}

const UserContext = createContext<UserContextType | undefined>(undefined);

export const UserProvider: React.FC<{ children: ReactNode }> = ({ children }) => {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState<boolean>(true);

  const checkAuthStatus = async () => {
    try {
      // Get the user ID from localStorage (if any)
      const storedUserId = localStorage.getItem('userId');
      
      if (storedUserId) {
        const userData = await apiRequest(`/api/users/${storedUserId}`);
        if (userData) {
          setUser(userData);
        }
      }
    } catch (error) {
      console.error('Auth check error:', error);
      localStorage.removeItem('userId');
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    checkAuthStatus();
  }, []);

  const login = async (username: string, password: string) => {
    try {
      setLoading(true);
      const userData = await apiRequest('/api/auth/login', {
        method: 'POST',
        body: JSON.stringify({ username, password }),
      });
      
      setUser(userData);
      localStorage.setItem('userId', userData.id.toString());
    } catch (error) {
      console.error('Login error:', error);
      throw error;
    } finally {
      setLoading(false);
    }
  };

  const logout = async () => {
    try {
      await apiRequest('/api/auth/logout', { method: 'POST' });
    } catch (error) {
      console.error('Logout error:', error);
    } finally {
      setUser(null);
      localStorage.removeItem('userId');
    }
  };

  const register = async (userData: RegisterData) => {
    try {
      setLoading(true);
      const newUser = await apiRequest('/api/users', {
        method: 'POST',
        body: JSON.stringify(userData),
      });
      
      await login(userData.username, userData.password);
      return newUser;
    } catch (error) {
      console.error('Registration error:', error);
      throw error;
    } finally {
      setLoading(false);
    }
  };

  return (
    <UserContext.Provider value={{ user, loading, login, logout, register }}>
      {children}
    </UserContext.Provider>
  );
};

export const useUser = (): UserContextType => {
  const context = useContext(UserContext);
  if (context === undefined) {
    throw new Error('useUser must be used within a UserProvider');
  }
  return context;
};
```

### client/src/components/TherapySession.tsx
```typescript
import { useEffect, useState } from 'react';
import { useParams, useLocation } from 'wouter';
import { useTherapySession } from '@/contexts/TherapySessionContext';
import { Button } from '@/components/ui/button';
import { Card, CardContent } from '@/components/ui/card';
import SessionControls from './SessionControls';
import VideoFeed from './VideoFeed';
import ChatPanel from './ChatPanel';
import MoodCheckModal from './modals/MoodCheckModal';
import { useToast } from '@/hooks/use-toast';

const TherapySession = () => {
  const params = useParams<{ id?: string }>();
  const [, setLocation] = useLocation();
  const { toast } = useToast();
  
  const { 
    activeSession, 
    isLoading, 
    isConnected,
    isTherapistTyping,
    startSession, 
    endSession, 
    sendMessage,
    sessionTime
  } = useTherapySession();
  
  const [micMuted, setMicMuted] = useState(false);
  const [videoOff, setVideoOff] = useState(false);
  const [isMoodModalOpen, setIsMoodModalOpen] = useState(false);
  const [isTherapistSpeaking, setIsTherapistSpeaking] = useState(false);
  
  useEffect(() => {
    // Auto-start session if ID is provided in URL
    if (params.id && !activeSession && !isLoading) {
      const sessionId = parseInt(params.id);
      startSession(sessionId);
    }
  }, [params.id, activeSession, isLoading, startSession]);
  
  useEffect(() => {
    // Open mood check modal when starting a new session
    if (!params.id && !activeSession && !isLoading) {
      setIsMoodModalOpen(true);
    }
  }, [params.id, activeSession, isLoading]);
  
  // Audio detection effect
  useEffect(() => {
    if (activeSession) {
      // Simulate therapist speaking state changes based on new messages
      const lastMessage = activeSession.messages[activeSession.messages.length - 1];
      if (lastMessage && lastMessage.role === 'therapist') {
        setIsTherapistSpeaking(true);
        
        // Simulate speech duration based on message length
        const speechDuration = Math.min(10, Math.max(3, lastMessage.content.length / 20)) * 1000;
        
        const timer = setTimeout(() => {
          setIsTherapistSpeaking(false);
        }, speechDuration);
        
        return () => clearTimeout(timer);
      }
    }
  }, [activeSession]);
  
  const formatSessionTime = () => {
    const minutes = Math.floor(sessionTime / 60);
    const seconds = sessionTime % 60;
    return `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
  };
  
  const handleToggleMic = () => {
    setMicMuted(!micMuted);
  };
  
  const handleToggleVideo = () => {
    setVideoOff(!videoOff);
  };
  
  const handleEndCall = async () => {
    try {
      await endSession();
      setLocation('/past-sessions');
      toast({
        title: "Session Ended",
        description: "Your therapy session has been completed.",
      });
    } catch (error) {
      console.error('Error ending session:', error);
    }
  };
  
  const handleStartSession = async (moodScore: number, notes: string) => {
    try {
      setIsMoodModalOpen(false);
      await startSession(undefined, notes);
    } catch (error) {
      console.error('Error starting session:', error);
    }
  };
  
  if (isLoading) {
    return (
      <div className="flex items-center justify-center min-h-[80vh]">
        <div className="w-10 h-10 border-4 border-primary border-solid rounded-full border-t-transparent animate-spin"></div>
      </div>
    );
  }
  
  if (!activeSession && !isMoodModalOpen) {
    return (
      <div className="flex flex-col items-center justify-center min-h-[80vh] gap-6">
        <h1 className="text-3xl font-bold">Start a Therapy Session</h1>
        <p className="text-muted-foreground text-center max-w-md">
          Begin your session with a brief mood check to help the therapist understand how you're feeling today.
        </p>
        <Button size="lg" onClick={() => setIsMoodModalOpen(true)}>
          Start Session
        </Button>
      </div>
    );
  }
  
  return (
    <>
      <MoodCheckModal 
        isOpen={isMoodModalOpen} 
        onClose={() => setIsMoodModalOpen(false)}
        onStartSession={handleStartSession}
      />
      
      {activeSession && (
        <div className="container mx-auto">
          <div className="grid grid-cols-1 lg:grid-cols-3 gap-6">
            {/* Session controls and info */}
            <div className="col-span-1 lg:col-span-3 flex items-center justify-between">
              <div>
                <h1 className="text-2xl font-bold">Therapy Session</h1>
                <p className="text-muted-foreground">
                  {activeSession.focus || 'General therapy session'}
                </p>
              </div>
              <div className="flex items-center gap-4">
                <div className="bg-muted px-4 py-2 rounded-md">
                  <span className="font-mono">{formatSessionTime()}</span>
                </div>
                <div className={`px-3 py-1 rounded-full text-sm ${
                  isConnected ? 'bg-green-100 text-green-800' : 'bg-amber-100 text-amber-800'
                }`}>
                  {isConnected ? 'Connected' : 'Connecting...'}
                </div>
              </div>
            </div>
            
            {/* Video feed */}
            <div className="col-span-1 lg:col-span-2">
              <Card>
                <CardContent className="p-4">
                  <VideoFeed 
                    isActive={isConnected && !videoOff} 
                    isTherapistSpeaking={isTherapistSpeaking}
                  />
                  <SessionControls 
                    micMuted={micMuted}
                    videoOff={videoOff}
                    onToggleMic={handleToggleMic}
                    onToggleVideo={handleToggleVideo}
                    onEndCall={handleEndCall}
                  />
                </CardContent>
              </Card>
            </div>
            
            {/* Chat panel */}
            <div className="col-span-1">
              <Card className="h-full">
                <CardContent className="p-4">
                  <ChatPanel 
                    messages={activeSession.messages} 
                    onSendMessage={sendMessage}
                    isTherapistTyping={isTherapistTyping}
                  />
                </CardContent>
              </Card>
            </div>
          </div>
        </div>
      )}
    </>
  );
};

export default TherapySession;
```

### client/src/components/ChatPanel.tsx
```typescript
import { useState, useRef, useEffect } from 'react';
import { Button } from '@/components/ui/button';
import { Textarea } from '@/components/ui/textarea';
import { SendIcon } from 'lucide-react';
import { Message } from '@/contexts/TherapySessionContext';
import { Avatar, AvatarFallback, AvatarImage } from '@/components/ui/avatar';
import { ScrollArea } from '@/components/ui/scroll-area';

interface ChatPanelProps {
  messages: Message[];
  onSendMessage: (content: string) => Promise<void>;
  isTherapistTyping: boolean;
}

const ChatPanel = ({ messages, onSendMessage, isTherapistTyping }: ChatPanelProps) => {
  const [inputValue, setInputValue] = useState('');
  const [isSending, setIsSending] = useState(false);
  const messagesEndRef = useRef<HTMLDivElement>(null);
  
  useEffect(() => {
    scrollToBottom();
  }, [messages, isTherapistTyping]);
  
  const scrollToBottom = () => {
    messagesEndRef.current?.scrollIntoView({ behavior: 'smooth' });
  };
  
  const handleSendMessage = async () => {
    if (inputValue.trim() === '' || isSending) return;
    
    try {
      setIsSending(true);
      await onSendMessage(inputValue);
      setInputValue('');
    } catch (error) {
      console.error('Error sending message:', error);
    } finally {
      setIsSending(false);
    }
  };
  
  const handleKeyDown = (e: React.KeyboardEvent) => {
    if (e.key === 'Enter' && !e.shiftKey) {
      e.preventDefault();
      handleSendMessage();
    }
  };
  
  const formatTime = (date: Date): string => {
    return date.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
  };
  
  return (
    <div className="flex flex-col h-full">
      <div className="text-lg font-semibold mb-2">
        Chat
      </div>
      
      <ScrollArea className="flex-1 pr-4 mb-4">
        <div className="space-y-4">
          {messages.map((message, index) => (
            <div 
              key={index} 
              className={`flex ${message.role === 'user' ? 'justify-end' : 'justify-start'}`}
            >
              <div 
                className={`
                  flex items-start gap-2 max-w-[80%]
                  ${message.role === 'user' ? 'flex-row-reverse' : 'flex-row'}
                `}
              >
                <Avatar className="h-8 w-8">
                  {message.role === 'therapist' ? (
                    <AvatarImage src="/therapist-avatar.jpg" alt="Dr. Michelle" />
                  ) : null}
                  <AvatarFallback>
                    {message.role === 'therapist' ? 'DR' : 'ME'}
                  </AvatarFallback>
                </Avatar>
                
                <div>
                  <div 
                    className={`
                      px-3 py-2 rounded-lg text-sm
                      ${message.role === 'user' 
                        ? 'bg-primary text-primary-foreground' 
                        : 'bg-muted text-foreground'}
                    `}
                  >
                    {message.content}
                  </div>
                  <div 
                    className={`
                      text-xs text-muted-foreground mt-1
                      ${message.role === 'user' ? 'text-right' : 'text-left'}
                    `}
                  >
                    {formatTime(new Date(message.timestamp))}
                  </div>
                </div>
              </div>
            </div>
          ))}
          
          {isTherapistTyping && (
            <div className="flex justify-start">
              <div className="flex items-start gap-2 max-w-[80%]">
                <Avatar className="h-8 w-8">
                  <AvatarFallback>DR</AvatarFallback>
                </Avatar>
                
                <div>
                  <div className="px-3 py-2 rounded-lg text-sm bg-muted text-foreground">
                    <div className="flex gap-1">
                      <div className="w-2 h-2 rounded-full bg-foreground animate-pulse"></div>
                      <div className="w-2 h-2 rounded-full bg-foreground animate-pulse delay-150"></div>
                      <div className="w-2 h-2 rounded-full bg-foreground animate-pulse delay-300"></div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          )}
          
          <div ref={messagesEndRef} />
        </div>
      </ScrollArea>
      
      <div className="mt-auto">
        <div className="flex items-end gap-2">
          <Textarea
            value={inputValue}
            onChange={(e) => setInputValue(e.target.value)}
            onKeyDown={handleKeyDown}
            placeholder="Type your message..."
            className="resize-none"
            disabled={isSending}
          />
          <Button 
            size="icon" 
            onClick={handleSendMessage}
            disabled={isSending || inputValue.trim() === ''}
          >
            <SendIcon className="h-4 w-4" />
          </Button>
        </div>
      </div>
    </div>
  );
};

export default ChatPanel;
```

### client/src/components/VideoFeed.tsx
```typescript
import { useEffect, useRef, useState } from 'react';
import { animateWaveform, createWaveform } from '@lib/avatarUtils';

interface VideoFeedProps {
  isActive: boolean;
  isTherapistSpeaking: boolean;
}

const VideoFeed = ({ isActive, isTherapistSpeaking }: VideoFeedProps) => {
  const avatarContainerRef = useRef<HTMLDivElement>(null);
  const waveformContainerRef = useRef<HTMLDivElement>(null);
  const [waveformBars, setWaveformBars] = useState<HTMLElement[]>([]);
  
  useEffect(() => {
    if (waveformContainerRef.current && waveformBars.length === 0) {
      const bars = createWaveform(waveformContainerRef.current);
      setWaveformBars(bars);
    }
  }, [waveformBars.length]);
  
  useEffect(() => {
    if (waveformBars.length > 0) {
      animateWaveform(waveformBars, isTherapistSpeaking);
    }
  }, [isTherapistSpeaking, waveformBars]);
  
  return (
    <div className="relative flex flex-col items-center justify-center bg-muted rounded-lg overflow-hidden" style={{ minHeight: '400px' }}>
      {isActive ? (
        <>
          <div className="avatar-container" ref={avatarContainerRef}>
            <img 
              src="https://fastly.picsum.photos/id/64/800/600.jpg?hmac=h_FHRGdD-QJc7Xgz0xFgxVfqXVUrhJnHJTnJevF1T_0" 
              alt="Therapist Avatar"
              className="avatar-image"
            />
          </div>
          
          <div 
            ref={waveformContainerRef} 
            className="waveform-container absolute bottom-4 left-1/2 transform -translate-x-1/2"
          ></div>
        </>
      ) : (
        <div className="text-center p-8">
          <div className="h-24 w-24 rounded-full bg-background mx-auto mb-4 flex items-center justify-center">
            <svg xmlns="http://www.w3.org/2000/svg" width="48" height="48" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round">
              <path d="M18.36 6.64a9 9 0 1 1-12.73 0"></path>
              <line x1="12" y1="2" x2="12" y2="12"></line>
            </svg>
          </div>
          <h3 className="text-xl font-semibold mb-2">Camera Off</h3>
          <p className="text-muted-foreground max-w-md">
            The video feed is currently disabled. Enable your camera to see the therapist.
          </p>
        </div>
      )}
      
      <div className="absolute top-3 right-3 bg-background/80 px-2 py-1 rounded text-xs">
        Dr. Michelle Carter
      </div>
      
      {isTherapistSpeaking && (
        <div className="absolute top-3 left-3 bg-green-500/20 text-green-700 px-2 py-1 rounded text-xs">
          Speaking
        </div>
      )}
    </div>
  );
};

export default VideoFeed;
```

### client/src/components/SessionControls.tsx
```typescript
import { Button } from '@/components/ui/button';
import { Mic, MicOff, Video, VideoOff, PhoneOff } from 'lucide-react';

interface SessionControlsProps {
  micMuted: boolean;
  videoOff: boolean;
  onToggleMic: () => void;
  onToggleVideo: () => void;
  onEndCall: () => void;
}

const SessionControls = ({
  micMuted,
  videoOff,
  onToggleMic,
  onToggleVideo,
  onEndCall
}: SessionControlsProps) => {
  return (
    <div className="flex justify-center items-center gap-4 mt-4 p-4 bg-card rounded-lg shadow-sm">
      <Button
        variant={micMuted ? 'secondary' : 'outline'}
        size="icon"
        onClick={onToggleMic}
        className="rounded-full h-12 w-12"
      >
        {micMuted ? <MicOff className="h-5 w-5" /> : <Mic className="h-5 w-5" />}
      </Button>
      
      <Button
        variant={videoOff ? 'secondary' : 'outline'}
        size="icon"
        onClick={onToggleVideo}
        className="rounded-full h-12 w-12"
      >
        {videoOff ? <VideoOff className="h-5 w-5" /> : <Video className="h-5 w-5" />}
      </Button>
      
      <Button
        variant="destructive"
        size="icon"
        onClick={onEndCall}
        className="rounded-full h-12 w-12"
      >
        <PhoneOff className="h-5 w-5" />
      </Button>
    </div>
  );
};

export default SessionControls;
```

### client/src/components/Sidebar.tsx
```typescript
import { useState } from 'react';
import { Link, useLocation } from 'wouter';
import { 
  Home, 
  Calendar, 
  Clock, 
  BarChart3, 
  Settings, 
  User, 
  HelpCircle, 
  LogOut,
  Menu
} from 'lucide-react';
import { Button } from '@/components/ui/button';
import { ScrollArea } from '@/components/ui/scroll-area';
import { Sheet, SheetContent, SheetTrigger } from '@/components/ui/sheet';
import { useUser } from '@/contexts/UserContext';
import { useToast } from '@/hooks/use-toast';
import { useIsMobile } from '@/hooks/use-mobile';

interface SidebarProps {
  collapsed?: boolean;
}

const Sidebar = ({ collapsed = false }: SidebarProps) => {
  const [location] = useLocation();
  const { user, logout } = useUser();
  const { toast } = useToast();
  const isMobile = useIsMobile();
  const [isOpen, setIsOpen] = useState(false);
  
  const handleLogout = async () => {
    await logout();
    toast({
      title: "Logged Out",
      description: "You have been successfully logged out.",
    });
  };
  
  const sidebarItems = [
    {
      icon: <Home className="h-5 w-5" />,
      label: 'Home',
      href: '/',
    },
    {
      icon: <Calendar className="h-5 w-5" />,
      label: 'Schedule',
      href: '/schedule',
    },
    {
      icon: <Clock className="h-5 w-5" />,
      label: 'Past Sessions',
      href: '/past-sessions',
    },
    {
      icon: <BarChart3 className="h-5 w-5" />,
      label: 'Progress',
      href: '/progress',
    },
    {
      icon: <User className="h-5 w-5" />,
      label: 'Profile',
      href: '/profile',
    },
    {
      icon: <Settings className="h-5 w-5" />,
      label: 'Settings',
      href: '/settings',
    },
    {
      icon: <HelpCircle className="h-5 w-5" />,
      label: 'Help',
      href: '/help',
    },
  ];
  
  const SidebarContent = () => (
    <div className="flex flex-col h-full py-4">
      <div className="px-4 py-2 mb-6">
        <h2 className={`text-2xl font-bold ${collapsed ? 'hidden' : 'block'}`}>
          <span className="logo-gradient">AI Therapy</span>
        </h2>
      </div>
      
      <ScrollArea className="flex-1">
        <div className="space-y-1 px-2">
          {sidebarItems.map((item) => (
            <Link key={item.href} href={item.href}>
              <a
                className={`
                  flex items-center px-3 py-2 rounded-md text-sm
                  ${location === item.href 
                    ? 'bg-primary text-primary-foreground' 
                    : 'text-foreground hover:bg-muted'}
                  ${collapsed ? 'justify-center' : 'justify-start'}
                  transition-colors
                `}
                onClick={() => isMobile && setIsOpen(false)}
              >
                {item.icon}
                {!collapsed && <span className="ml-3">{item.label}</span>}
              </a>
            </Link>
          ))}
        </div>
      </ScrollArea>
      
      <div className="mt-auto px-4 py-2">
        {user && (
          <div className={`flex items-center ${collapsed ? 'justify-center' : 'justify-start'} mb-4`}>
            <div className="h-8 w-8 rounded-full bg-muted flex items-center justify-center overflow-hidden">
              {user.profileImageUrl ? (
                <img src={user.profileImageUrl} alt={user.fullName} className="h-full w-full object-cover" />
              ) : (
                <span className="text-xs font-medium">{user.fullName.charAt(0)}</span>
              )}
            </div>
            {!collapsed && (
              <div className="ml-3">
                <p className="text-sm font-medium">{user.fullName}</p>
                <p className="text-xs text-muted-foreground">{user.username}</p>
              </div>
            )}
          </div>
        )}
        
        <Button 
          variant="outline" 
          className={`${collapsed ? 'w-10 h-10 p-0' : 'w-full'}`}
          onClick={handleLogout}
        >
          <LogOut className="h-4 w-4" />
          {!collapsed && <span className="ml-2">Logout</span>}
        </Button>
      </div>
    </div>
  );
  
  // For mobile devices, render a sidebar in a sheet
  if (isMobile) {
    return (
      <>
        <div className="fixed top-0 left-0 right-0 z-10 flex items-center justify-between p-4 bg-background/80 backdrop-blur-sm border-b">
          <h2 className="text-xl font-bold">
            <span className="logo-gradient">AI Therapy</span>
          </h2>
          
          <Sheet open={isOpen} onOpenChange={setIsOpen}>
            <SheetTrigger asChild>
              <Button variant="ghost" size="icon">
                <Menu className="h-5 w-5" />
              </Button>
            </SheetTrigger>
            <SheetContent side="left" className="p-0 w-[280px]">
              <SidebarContent />
            </SheetContent>
          </Sheet>
        </div>
        <div className="h-16" />
      </>
    );
  }
  
  // For desktop devices, render a normal sidebar
  return (
    <aside className={`${collapsed ? 'w-16' : 'w-64'} h-screen bg-background border-r`}>
      <SidebarContent />
    </aside>
  );
};

export default Sidebar;
```

### client/src/components/modals/MoodCheckModal.tsx
```typescript
import { useState } from 'react';
import { 
  Dialog, 
  DialogContent, 
  DialogHeader, 
  DialogTitle, 
  DialogDescription 
} from '@/components/ui/dialog';
import { Button } from '@/components/ui/button';
import { Textarea } from '@/components/ui/textarea';
import { Slider } from '@/components/ui/slider';

interface MoodCheckModalProps {
  isOpen: boolean;
  onClose: () => void;
  onStartSession: (moodScore: number, notes: string) => void;
  sessionId?: number;
}

const MoodCheckModal = ({ isOpen, onClose, onStartSession, sessionId }: MoodCheckModalProps) => {
  const [moodScore, setMoodScore] = useState(5);
  const [notes, setNotes] = useState('');
  
  const getMoodLabel = (score: number) => {
    switch (score) {
      case 1: return "Very Poor";
      case 2: return "Poor";
      case 3: return "Neutral";
      case 4: return "Good";
      case 5: return "Very Good";
      default: return "Neutral";
    }
  };
  
  const getMoodColor = (score: number) => {
    switch (score) {
      case 1: return "text-red-500";
      case 2: return "text-orange-500";
      case 3: return "text-amber-500";
      case 4: return "text-lime-500";
      case 5: return "text-green-500";
      default: return "text-amber-500";
    }
  };
  
  const handleStartSession = () => {
    onStartSession(moodScore, notes);
  };
  
  return (
    <Dialog open={isOpen} onOpenChange={onClose}>
      <DialogContent className="sm:max-w-md">
        <DialogHeader>
          <DialogTitle>How are you feeling today?</DialogTitle>
          <DialogDescription>
            This will help your AI therapist tailor the session to your current emotional state.
          </DialogDescription>
        </DialogHeader>
        
        <div className="py-4 space-y-6">
          <div className="space-y-4">
            <div className="text-center">
              <span className={`text-3xl font-bold ${getMoodColor(moodScore)}`}>
                {getMoodLabel(moodScore)}
              </span>
            </div>
            
            <Slider
              value={[moodScore]}
              min={1}
              max={5}
              step={1}
              onValueChange={(value) => setMoodScore(value[0])}
              className="py-4"
            />
            
            <div className="flex justify-between text-sm text-muted-foreground">
              <span>Very Poor</span>
              <span>Very Good</span>
            </div>
          </div>
          
          <div className="space-y-2">
            <label htmlFor="notes" className="text-sm font-medium">
              What's on your mind today? (optional)
            </label>
            <Textarea
              id="notes"
              placeholder="Share a few thoughts about what you'd like to discuss..."
              value={notes}
              onChange={(e) => setNotes(e.target.value)}
              rows={4}
            />
          </div>
        </div>
        
        <div className="flex justify-end space-x-2">
          <Button variant="outline" onClick={onClose}>
            Cancel
          </Button>
          <Button onClick={handleStartSession}>
            Start Session
          </Button>
        </div>
      </DialogContent>
    </Dialog>
  );
};

export default MoodCheckModal;
```

### client/src/lib/avatarUtils.ts
```typescript
export const animateAvatar = (audioElement: HTMLAudioElement, canvasContext: CanvasRenderingContext2D) => {
  // Create analyzer node
  const audioContext = new AudioContext();
  const analyzer = audioContext.createAnalyser();
  const source = audioContext.createMediaElementSource(audioElement);
  
  source.connect(analyzer);
  analyzer.connect(audioContext.destination);
  
  analyzer.fftSize = 256;
  const bufferLength = analyzer.frequencyBinCount;
  const dataArray = new Uint8Array(bufferLength);
  
  const canvasWidth = canvasContext.canvas.width;
  const canvasHeight = canvasContext.canvas.height;
  
  function animate() {
    requestAnimationFrame(animate);
    
    analyzer.getByteFrequencyData(dataArray);
    
    canvasContext.clearRect(0, 0, canvasWidth, canvasHeight);
    
    // Calculate average frequency to drive mouth movement
    let sum = 0;
    for (let i = 0; i < bufferLength; i++) {
      sum += dataArray[i];
    }
    const average = sum / bufferLength;
    
    // Use the average to animate speaking
    const mouthOpenAmount = Math.min(30, average / 5);
    
    // Draw face here...
    
    // Example: Draw a simple face with responsive mouth
    canvasContext.fillStyle = '#f0f0f0';
    canvasContext.beginPath();
    canvasContext.arc(canvasWidth / 2, canvasHeight / 2, 100, 0, 2 * Math.PI);
    canvasContext.fill();
    
    // Eyes
    canvasContext.fillStyle = '#333';
    canvasContext.beginPath();
    canvasContext.arc(canvasWidth / 2 - 30, canvasHeight / 2 - 20, 10, 0, 2 * Math.PI);
    canvasContext.arc(canvasWidth / 2 + 30, canvasHeight / 2 - 20, 10, 0, 2 * Math.PI);
    canvasContext.fill();
    
    // Mouth (changes with audio)
    canvasContext.beginPath();
    canvasContext.ellipse(
      canvasWidth / 2, 
      canvasHeight / 2 + 30, 
      30, 
      mouthOpenAmount, 
      0, 
      0, 
      2 * Math.PI
    );
    canvasContext.fill();
  }
  
  animate();
};

export const createWaveform = (container: HTMLElement, numBars: number = 8): HTMLElement[] => {
  // Clear existing children
  while (container.firstChild) {
    container.removeChild(container.firstChild);
  }
  
  const bars: HTMLElement[] = [];
  
  for (let i = 0; i < numBars; i++) {
    const bar = document.createElement('div');
    bar.className = 'waveform-bar';
    bar.style.height = '5px';
    container.appendChild(bar);
    bars.push(bar);
  }
  
  return bars;
};

export const animateWaveform = (bars: HTMLElement[], isSpeaking: boolean): void => {
  if (isSpeaking) {
    // Create animation frames for each bar
    bars.forEach((bar, index) => {
      // Random height for animation (between 5px and 30px)
      const animateBar = () => {
        if (!isSpeaking) return;
        
        const height = Math.floor(Math.random() * 25) + 5;
        bar.style.height = `${height}px`;
        
        // Slight delay between animations for smoother effect
        setTimeout(() => {
          if (document.body.contains(bar)) {
            requestAnimationFrame(animateBar);
          }
        }, 100 + (index * 50) % 200);
      };
      
      animateBar();
    });
  } else {
    // Reset all bars to minimal height
    bars.forEach(bar => {
      bar.style.height = '5px';
    });
  }
};
```

