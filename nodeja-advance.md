## Additional Modern Node.js Interview Questions (2024-2025)

### Q61: Explain Node.js Worker Threads and Their Use Cases

```javascript
const { Worker, isMainThread, parentPort } = require('worker_threads');

if (isMainThread) {
    // Main thread code
    const worker = new Worker(`
        const { parentPort } = require('worker_threads');
        parentPort.on('message', (data) => {
            // Heavy computation
            const result = performHeavyComputation(data);
            parentPort.postMessage(result);
        });
    `, { eval: true });

    worker.on('message', (result) => {
        console.log('Computation result:', result);
    });

    worker.postMessage(dataToProcess);
} else {
    // Worker thread code
    parentPort.on('message', (data) => {
        // Process data
    });
}
```

### Q62: How do you implement GraphQL in a Node.js Application?

```javascript
const { ApolloServer, gql } = require('apollo-server-express');

// Schema definition
const typeDefs = gql`
    type User {
        id: ID!
        name: String!
        email: String!
        posts: [Post!]!
    }

    type Post {
        id: ID!
        title: String!
        content: String!
        author: User!
    }

    type Query {
        user(id: ID!): User
        posts: [Post!]!
    }
`;

// Resolvers
const resolvers = {
    Query: {
        user: async (_, { id }) => {
            return await User.findById(id);
        },
        posts: async () => {
            return await Post.find();
        }
    },
    User: {
        posts: async (parent) => {
            return await Post.find({ authorId: parent.id });
        }
    }
};

// Apollo Server setup
const server = new ApolloServer({ 
    typeDefs, 
    resolvers,
    context: ({ req }) => ({
        user: req.user // From auth middleware
    })
});
```

### Q63: Implementing WebAssembly with Node.js

```javascript
const fs = require('fs');
const util = require('util');
const readFile = util.promisify(fs.readFile);

async function loadWebAssembly() {
    const wasmBuffer = await readFile('math.wasm');
    const wasmModule = await WebAssembly.compile(wasmBuffer);
    const instance = await WebAssembly.instantiate(wasmModule);
    
    return instance.exports;
}

// Usage
async function performCalculation() {
    const wasm = await loadWebAssembly();
    const result = wasm.calculate(10, 20);
    console.log('WASM calculation result:', result);
}
```

### Q64: Implementing Server-Sent Events (SSE)

```javascript
const express = require('express');
const app = express();

app.get('/events', (req, res) => {
    res.setHeader('Content-Type', 'text/event-stream');
    res.setHeader('Cache-Control', 'no-cache');
    res.setHeader('Connection', 'keep-alive');

    // Send initial connection message
    res.write('data: Connected to event stream\n\n');

    // Setup interval for regular updates
    const intervalId = setInterval(() => {
        const data = {
            timestamp: Date.now(),
            value: Math.random()
        };
        res.write(`data: ${JSON.stringify(data)}\n\n`);
    }, 1000);

    // Clean up on client disconnect
    req.on('close', () => {
        clearInterval(intervalId);
    });
});
```

### Q65: Implementing Circuit Breaker Pattern

```javascript
class CircuitBreaker {
    constructor(request, options = {}) {
        this.request = request;
        this.state = 'CLOSED';
        this.failureCount = 0;
        this.failureThreshold = options.failureThreshold || 5;
        this.resetTimeout = options.resetTimeout || 60000;
        this.lastFailureTime = null;
    }

    async execute(params) {
        if (this.state === 'OPEN') {
            if (Date.now() - this.lastFailureTime >= this.resetTimeout) {
                this.state = 'HALF-OPEN';
            } else {
                throw new Error('Circuit breaker is OPEN');
            }
        }

        try {
            const response = await this.request(params);
            this.success();
            return response;
        } catch (error) {
            this.failure();
            throw error;
        }
    }

    success() {
        this.failureCount = 0;
        this.state = 'CLOSED';
    }

    failure() {
        this.failureCount++;
        this.lastFailureTime = Date.now();

        if (this.failureCount >= this.failureThreshold) {
            this.state = 'OPEN';
        }
    }
}

// Usage
const apiCall = new CircuitBreaker(async (params) => {
    const response = await axios.get(params.url);
    return response.data;
});
```

### Q66: Implementing Graceful Shutdown

```javascript
class GracefulShutdown {
    constructor(server, options = {}) {
        this.server = server;
        this.options = {
            timeout: options.timeout || 30000,
            signals: options.signals || ['SIGTERM', 'SIGINT']
        };
        this.connections = new Set();
        this.init();
    }

    init() {
        // Track connections
        this.server.on('connection', (connection) => {
            this.connections.add(connection);
            connection.on('close', () => {
                this.connections.delete(connection);
            });
        });

        // Handle shutdown signals
        this.options.signals.forEach(signal => {
            process.on(signal, () => this.shutdown());
        });
    }

    async shutdown() {
        console.log('Initiating graceful shutdown...');

        // Stop accepting new connections
        this.server.close(() => {
            console.log('Server closed');
        });

        // Close existing connections
        for (const connection of this.connections) {
            connection.end();
        }

        // Close database connections
        await mongoose.disconnect();
        
        // Close other resources (Redis, etc.)
        await redis.quit();

        // Set timeout for force shutdown
        setTimeout(() => {
            console.error('Could not close connections in time, forcefully shutting down');
            process.exit(1);
        }, this.options.timeout);
    }
}
```

### Q67: Implementing Request Tracing

```javascript
const { v4: uuidv4 } = require('uuid');

const requestTracer = (req, res, next) => {
    const traceId = req.headers['x-trace-id'] || uuidv4();
    const requestStart = Date.now();

    // Attach trace ID to request
    req.traceId = traceId;

    // Override response methods to add tracing
    const originalJson = res.json;
    res.json = function(body) {
        const responseTime = Date.now() - requestStart;
        
        // Add tracing headers
        res.set({
            'X-Trace-Id': traceId,
            'X-Response-Time': `${responseTime}ms`
        });

        // Log request details
        console.log({
            traceId,
            method: req.method,
            path: req.path,
            statusCode: res.statusCode,
            responseTime,
            userAgent: req.get('user-agent')
        });

        return originalJson.call(this, body);
    };

    next();
};
```

### Q68: Implementing API Versioning

```javascript
const express = require('express');
const app = express();

// Version middleware
const versionMiddleware = (req, res, next) => {
    const version = req.headers['accept-version'] || '1';
    req.apiVersion = version;
    next();
};

// Version-specific routes
const routeHandler = (version) => {
    const router = express.Router();

    if (version === '1') {
        router.get('/users', (req, res) => {
            res.json({ version: '1', data: [] });
        });
    } else if (version === '2') {
        router.get('/users', (req, res) => {
            res.json({ 
                version: '2', 
                data: [],
                metadata: {}
            });
        });
    }

    return router;
};

app.use(versionMiddleware);
app.use('/v1', routeHandler('1'));
app.use('/v2', routeHandler('2'));
```

### Q69: Implementing WebSocket with Custom Protocol

```javascript
const WebSocket = require('ws');
const wss = new WebSocket.Server({ port: 8080 });

// Custom protocol handler
class CustomWebSocketProtocol {
    static handleMessage(ws, message) {
        try {
            const data = JSON.parse(message);
            
            switch (data.type) {
                case 'subscribe':
                    this.handleSubscribe(ws, data);
                    break;
                case 'publish':
                    this.handlePublish(ws, data);
                    break;
                default:
                    ws.send(JSON.stringify({
                        type: 'error',
                        message: 'Unknown message type'
                    }));
            }
        } catch (error) {
            ws.send(JSON.stringify({
                type: 'error',
                message: 'Invalid message format'
            }));
        }
    }

    static handleSubscribe(ws, data) {
        ws.subscriptions = ws.subscriptions || new Set();
        ws.subscriptions.add(data.channel);
        
        ws.send(JSON.stringify({
            type: 'subscribed',
            channel: data.channel
        }));
    }

    static handlePublish(ws, data) {
        wss.clients.forEach(client => {
            if (client.subscriptions?.has(data.channel)) {
                client.send(JSON.stringify({
                    type: 'message',
                    channel: data.channel,
                    data: data.payload
                }));
            }
        });
    }
}

wss.on('connection', (ws) => {
    ws.on('message', (message) => {
        CustomWebSocketProtocol.handleMessage(ws, message);
    });
});
```

### Q70: Implementing Service Discovery

```javascript
const consul = require('consul')();

class ServiceRegistry {
    constructor(serviceName, port) {
        this.serviceName = serviceName;
        this.serviceId = `${serviceName}-${port}`;
        this.port = port;
    }

    async register() {
        try {
            await consul.agent.service.register({
                name: this.serviceName,
                id: this.serviceId,
                port: this.port,
                check: {
                    http: `http://localhost:${this.port}/health`,
                    interval: '10s'
                }
            });
            console.log(`Service registered: ${this.serviceId}`);
        } catch (error) {
            console.error('Service registration failed:', error);
            throw error;
        }
    }

    async deregister() {
        try {
            await consul.agent.service.deregister(this.serviceId);
            console.log(`Service deregistered: ${this.serviceId}`);
        } catch (error) {
            console.error('Service deregistration failed:', error);
            throw error;
        }
    }

    async discover(serviceName) {
        try {
            const services = await consul.agent.service.list();
            return Object.values(services).filter(
                service => service.Service === serviceName
            );
        } catch (error) {
            console.error('Service discovery failed:', error);
            throw error;
        }
    }
}
```
[Previous content remains the same...]

## Specialized Node.js Interview Questions

### Memory Management and Performance

### Q71: Explain Memory Leaks in Node.js and How to Detect Them

```javascript
// Memory Leak Detection Implementation
const heapdump = require('heapdump');
const memwatch = require('@airbnb/node-memwatch');

class MemoryMonitor {
    constructor(threshold = 100) {
        this.threshold = threshold;
        this.baselineMemory = null;
        this.init();
    }

    init() {
        // Create baseline
        this.baselineMemory = process.memoryUsage().heapUsed;
        
        // Listen for memory leaks
        memwatch.on('leak', (info) => {
            console.log('Memory leak detected:', info);
            
            // Generate heap snapshot
            heapdump.writeSnapshot(`./heapdump-${Date.now()}.heapsnapshot`);
        });

        // Monitor memory usage
        setInterval(() => this.checkMemory(), 30000);
    }

    checkMemory() {
        const currentMemory = process.memoryUsage().heapUsed;
        const memoryGrowth = ((currentMemory - this.baselineMemory) / this.baselineMemory) * 100;

        if (memoryGrowth > this.threshold) {
            console.warn(`Memory growth exceeded threshold: ${memoryGrowth.toFixed(2)}%`);
            this.generateReport();
        }
    }

    generateReport() {
        const memoryUsage = process.memoryUsage();
        return {
            heapTotal: this.formatBytes(memoryUsage.heapTotal),
            heapUsed: this.formatBytes(memoryUsage.heapUsed),
            external: this.formatBytes(memoryUsage.external),
            rss: this.formatBytes(memoryUsage.rss)
        };
    }

    formatBytes(bytes) {
        return (bytes / 1024 / 1024).toFixed(2) + ' MB';
    }
}

// Example of common memory leak
class LeakyClass {
    constructor() {
        this.cache = new Map();
        this.interval = setInterval(() => {
            this.cache.set(Date.now(), { data: 'some data' });
        }, 100);
    }

    // Memory leak fix
    destroy() {
        clearInterval(this.interval);
        this.cache.clear();
    }
}
```

### Q72: Implementing Custom Stream Transformations

```javascript
const { Transform } = require('stream');

// Custom CSV to JSON transformer
class CSVToJSON extends Transform {
    constructor(options = {}) {
        options.objectMode = true;
        super(options);
        this.headers = null;
        this.buffer = '';
    }

    _transform(chunk, encoding, callback) {
        try {
            this.buffer += chunk.toString();
            const lines = this.buffer.split('\n');
            
            // Keep the last partial line in buffer
            this.buffer = lines.pop();

            lines.forEach(line => {
                const values = line.trim().split(',');

                if (!this.headers) {
                    this.headers = values;
                } else {
                    const obj = {};
                    this.headers.forEach((header, index) => {
                        obj[header.trim()] = values[index]?.trim();
                    });
                    this.push(JSON.stringify(obj) + '\n');
                }
            });

            callback();
        } catch (error) {
            callback(error);
        }
    }

    _flush(callback) {
        if (this.buffer) {
            const values = this.buffer.trim().split(',');
            const obj = {};
            this.headers.forEach((header, index) => {
                obj[header.trim()] = values[index]?.trim();
            });
            this.push(JSON.stringify(obj) + '\n');
        }
        callback();
    }
}

// Usage example
const fs = require('fs');
const csvToJson = new CSVToJSON();

fs.createReadStream('input.csv')
    .pipe(csvToJson)
    .pipe(fs.createWriteStream('output.json'));
```

### Q73: Implementing Custom Error Handling System

```javascript
// Custom error types
class AppError extends Error {
    constructor(message, statusCode, errorCode) {
        super(message);
        this.statusCode = statusCode;
        this.errorCode = errorCode;
        this.isOperational = true;
        Error.captureStackTrace(this, this.constructor);
    }
}

// Error handler middleware with detailed logging
class ErrorHandler {
    constructor() {
        this.errorLogger = this.createErrorLogger();
    }

    createErrorLogger() {
        const winston = require('winston');
        return winston.createLogger({
            level: 'error',
            format: winston.format.combine(
                winston.format.timestamp(),
                winston.format.json()
            ),
            transports: [
                new winston.transports.File({ filename: 'error.log' }),
                new winston.transports.Console()
            ]
        });
    }

    handle(err, req, res, next) {
        const error = {
            message: err.message,
            code: err.errorCode || 'INTERNAL_ERROR',
            status: err.statusCode || 500,
            timestamp: new Date().toISOString(),
            path: req.path,
            method: req.method,
            requestId: req.id,
            stack: process.env.NODE_ENV === 'development' ? err.stack : undefined
        };

        // Log error
        this.errorLogger.error({
            ...error,
            headers: req.headers,
            query: req.query,
            body: req.body
        });

        // Send response
        res.status(error.status).json({
            error: {
                message: error.message,
                code: error.code,
                requestId: error.requestId
            }
        });
    }

    async handleUnhandledRejection(err) {
        this.errorLogger.error('Unhandled Rejection:', err);
        // Graceful shutdown after logging
        process.exit(1);
    }
}
```

### Q74: Implementing Advanced Caching Strategies

```javascript
const Redis = require('ioredis');
const LRU = require('lru-cache');

class CacheManager {
    constructor() {
        this.redis = new Redis({
            host: process.env.REDIS_HOST,
            port: process.env.REDIS_PORT,
            retryStrategy: (times) => {
                const delay = Math.min(times * 50, 2000);
                return delay;
            }
        });

        this.localCache = new LRU({
            max: 500,
            maxAge: 1000 * 60 * 5 // 5 minutes
        });
    }

    async get(key, fetchData, options = {}) {
        // Try local cache first
        const localData = this.localCache.get(key);
        if (localData) {
            return localData;
        }

        // Try Redis cache
        try {
            const redisData = await this.redis.get(key);
            if (redisData) {
                const parsed = JSON.parse(redisData);
                this.localCache.set(key, parsed);
                return parsed;
            }
        } catch (error) {
            console.error('Redis error:', error);
        }

        // Fetch fresh data
        const data = await fetchData();
        
        // Update both caches
        this.localCache.set(key, data);
        await this.redis.set(
            key, 
            JSON.stringify(data), 
            'EX', 
            options.ttl || 3600
        );

        return data;
    }

    async invalidate(pattern) {
        // Clear local cache entries
        this.localCache.reset();

        // Clear Redis cache entries
        const keys = await this.redis.keys(pattern);
        if (keys.length > 0) {
            await this.redis.del(keys);
        }
    }

    // Implements cache warming
    async warmUp(keys, fetchData) {
        const promises = keys.map(async (key) => {
            const data = await fetchData(key);
            await this.set(key, data);
        });

        await Promise.all(promises);
    }
}
```

### Q75: Implementing Advanced Logging System

```javascript
const winston = require('winston');
const ElasticsearchTransport = require('winston-elasticsearch');

class Logger {
    constructor(options = {}) {
        this.options = {
            service: options.service || 'default-service',
            level: options.level || 'info',
            elasticsearch: options.elasticsearch || {
                node: 'http://localhost:9200'
            }
        };

        this.logger = this.createLogger();
    }

    createLogger() {
        const logger = winston.createLogger({
            defaultMeta: { service: this.options.service },
            format: winston.format.combine(
                winston.format.timestamp(),
                winston.format.errors({ stack: true }),
                winston.format.metadata(),
                winston.format.json()
            ),
            transports: [
                new winston.transports.Console({
                    format: winston.format.combine(
                        winston.format.colorize(),
                        winston.format.simple()
                    )
                }),
                new ElasticsearchTransport({
                    level: 'info',
                    index: `logs-${this.options.service}`,
                    clientOpts: this.options.elasticsearch
                })
            ]
        });

        return logger;
    }

    log(level, message, meta = {}) {
        this.logger.log(level, message, {
            timestamp: new Date().toISOString(),
            ...meta
        });
    }

    // Structured logging methods
    logHTTPRequest(req, res, responseTime) {
        this.log('info', 'HTTP Request', {
            method: req.method,
            url: req.url,
            status: res.statusCode,
            responseTime,
            userAgent: req.get('user-agent'),
            ip: req.ip
        });
    }

    logDatabaseQuery(query, params, duration) {
        this.log('debug', 'Database Query', {
            query,
            params,
            duration
        });
    }

    logError(error, context = {}) {
        this.log('error', error.message, {
            stack: error.stack,
            ...context
        });
    }

    // Performance monitoring
    logPerformanceMetric(metric, value, tags = {}) {
        this.log('info', 'Performance Metric', {
            metric,
            value,
            tags
        });
    }
}
```

### Q76: Advanced Authentication Implementation

```javascript
const jwt = require('jsonwebtoken');
const bcrypt = require('bcrypt');
const crypto = require('crypto');

class AuthenticationService {
    constructor(userModel, options = {}) {
        this.userModel = userModel;
        this.options = {
            jwtSecret: options.jwtSecret || process.env.JWT_SECRET,
            jwtExpiry: options.jwtExpiry || '15m',
            refreshTokenExpiry: options.refreshTokenExpiry || '7d',
            maxLoginAttempts: options.maxLoginAttempts || 5,
            lockoutDuration: options.lockoutDuration || 15 * 60 * 1000 // 15 minutes
        };
    }

    async login(email, password) {
        const user = await this.userModel.findOne({ email });
        
        if (!user) {
            throw new AppError('Invalid credentials', 401);
        }

        // Check account lockout
        if (user.isLocked && user.lockUntil > Date.now()) {
            throw new AppError('Account is locked. Try again later', 423);
        }

        // Verify password
        const isValid = await bcrypt.compare(password, user.password);
        if (!isValid) {
            await this.handleFailedLogin(user);
            throw new AppError('Invalid credentials', 401);
        }

        // Reset login attempts on successful login
        await this.resetLoginAttempts(user);

        // Generate tokens
        const { accessToken, refreshToken } = await this.generateTokens(user);

        return {
            accessToken,
            refreshToken,
            user: this.sanitizeUser(user)
        };
    }

    async handleFailedLogin(user) {
        user.loginAttempts += 1;
        
        if (user.loginAttempts >= this.options.maxLoginAttempts) {
            user.isLocked = true;
            user.lockUntil = Date.now() + this.options.lockoutDuration;
        }
        
        await user.save();
    }

    async resetLoginAttempts(user) {
        user.loginAttempts = 0;
        user.isLocked = false;
        user.lockUntil = null;
        await user.save();
    }

    async generateTokens(user) {
        const accessToken = jwt.sign(
            { id: user.id, role: user.role },
            this.options.jwtSecret,
            { expiresIn: this.options.jwtExpiry }
        );

        const refreshToken = crypto.randomBytes(40).toString('hex');
        
        // Store refresh token
        await this.userModel.updateOne(
            { _id: user._id },
            {
                $set: {
                    refreshToken,
                    refreshTokenExpiresAt: new Date(Date.now() + ms(this.options.refreshTokenExpiry))
                }
            }
        );

        return { accessToken, refreshToken };
    }

    async refreshToken(token) {
        const user = await this.userModel.findOne({
            refreshToken: token,
            refreshTokenExpiresAt: { $gt: new Date() }
        });

        if (!user) {
            throw new AppError('Invalid refresh token', 401);
        }

        return this.generateTokens(user);
    }

    async validateToken(token) {
        try {
            const decoded = jwt.verify(token, this.options.jwtSecret);
            const user = await this.userModel.findById(decoded.id);
            
            if (!user) {
                throw new Error('User not found');
            }

            return this.sanitizeUser(user);
        } catch (error) {
            throw new AppError('Invalid token', 401);
        }
    }

    sanitizeUser(user) {
        const { password, refreshToken, ...sanitizedUser } = user.toObject();
        return sanitizedUser;
    }
}
```

These implementations provide more detailed, production-ready code examples that cover:

1. Memory leak detection and monitoring
2. Advanced stream processing
3. Comprehensive error handling
4. Multi-level caching strategies
5. Structured logging with Elasticsearch integration
6. Secure authentication with rate limiting and account lockout

Would you like me to:
1. Add more specific technical areas?
2. Provide more implementation details for any particular area?
3. Add examples of how these implementations work together in a real application?

These additional questions cover modern Node.js development practices and patterns that are becoming increasingly important in 2024-2025. They focus on:

1. Performance optimization with Worker Threads
2. Modern API development with GraphQL
3. WebAssembly integration
4. Real-time features with SSE and WebSocket
5. Resilience patterns (Circuit Breaker)
6. Operational concerns (Graceful Shutdown)
7. Observability (Request Tracing)
8. API design (Versioning)
9. Microservices patterns (Service Discovery)

